# CREATE EXTERNAL TABLE<a name="r_CREATE_EXTERNAL_TABLE"></a>

Creates a new external table in the specified schema\. All external tables must be created in an external schema\. Search path is not supported for external schemas and external tables\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

To create external tables, you must be the owner of the external schema or a superuser\. To transfer ownership of an external schema, use ALTER SCHEMA to change the owner\. Access to external tables is controlled by access to the external schema\. You can't [GRANT](r_GRANT.md) or [REVOKE](r_REVOKE.md) permissions on an external table\. Instead, grant or revoke USAGE on the external schema\.

In addition to external tables created using the CREATE EXTERNAL TABLE command, Amazon Redshift can reference external tables defined in an AWS Glue or Amazon Athena data catalog or a Hive metastore\. Use the [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md) command to register an external database defined in an AWS Glue or Athena data catalog or Hive metastore and make the external tables available for use in Amazon Redshift\. If the external table exists in an AWS Glue or Athena data catalog or Hive metastore, you don't need to create the table using CREATE EXTERNAL TABLE\. To view external tables, query the [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) system view\. 

You can query an external table using the same SELECT syntax you use with other Amazon Redshift tables\. External tables are read\-only\. You can't write to an external table\.

To create a view with an external table, include the WITH NO SCHEMA BINDING clause in the [CREATE VIEW](r_CREATE_VIEW.md) statement\.

You can't execute CREATE EXTERNAL TABLE inside a transaction \(BEGIN … END\)\.

## Syntax<a name="r_CREATE_EXTERNAL_TABLE-synopsis"></a>

```
CREATE EXTERNAL TABLE
external_schema.table_name  
(column_name data_type [, …] )
[ PARTITIONED BY (col_name data_type [, … ] )] 
[ { ROW FORMAT DELIMITED row_format |
  ROW FORMAT SERDE 'serde_name' 
  [ WITH SERDEPROPERTIES ( 'property_name' = 'property_value' [, ...] ) ] } ]
STORED AS file_format
LOCATION { 's3://bucket/folder/' | 's3://bucket/manifest_file' }
[ TABLE PROPERTIES ( 'property_name'='property_value' [, ...] ) ]
```

## Parameters<a name="r_CREATE_EXTERNAL_TABLE-parameters"></a>

 *external\_schema\.table\_name*   
The name of the table to be created, qualified by an external schema name\. External tables must be created in an external schema\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.  
The maximum length for the table name is 127 bytes; longer names are truncated to 127 bytes\. You can use UTF\-8 multibyte characters up to a maximum of four bytes\. Amazon Redshift enforces a limit of 9,900 tables per cluster, including user\-defined temporary tables and temporary tables created by Amazon Redshift during query processing or system maintenance\. Optionally, you can qualify the table name with the database name\. In the following example, the database name is `spectrum_db` , the external schema name is `spectrum_schema`, and the table name is `test`\.  

```
create external table spectrum_db.spectrum_schema.test (c1 int)
stored as textfile 
location 's3://mybucket/myfolder/';
```
If the database or schema specified doesn't exist, the table is not created, and the statement returns an error\. You can't create tables or views in the system databases `template0`, `template1`, and `padb_harvest`\.  
The table name must be a unique name for the specified schema\.   
For more information about valid names, see [Names and Identifiers](r_names.md)\.

\( *column\_name* *data\_type* \)  
The name and data type of each column being created\.  
The maximum length for the column name is 127 bytes; longer names are truncated to 127 bytes\. You can use UTF\-8 multibyte characters up to a maximum of four bytes\. You can't specify column names `"$path"` or `"$size"`\. For more information about valid names, see [Names and Identifiers](r_names.md)\.  
By default, Amazon Redshift creates external tables with the pseudocolumns `$path` and `$size`\. You can disable creation of pseudocolumns for a session by setting the `spectrum_enable_pseudo_columns` configuration parameter to `false`\. For more information, see [Pseudocolumns ](#r_CREATE_EXTERNAL_TABLE_usage-pseudocolumns)\.  
If pseudocolumns are enabled, the maximum number of columns you can define in a single table is 1,598\. If pseudocolumns are not enabled, the maximum number of columns you can define in a single table is 1,600\.   
If you are creating a "wide table," make sure that your list of columns doesn't exceed row\-width boundaries for intermediate results during loads and query processing\. For more information, see [Usage Notes](r_CREATE_TABLE_usage.md)\.

 *data\_type*   
The following [data types](c_Supported_data_types.md) are supported:  
+ SMALLINT \(INT2\)
+ INTEGER \(INT, INT4\)
+ BIGINT \(INT8\)
+ DECIMAL \(NUMERIC\)
+ REAL \(FLOAT4\)
+ DOUBLE PRECISION \(FLOAT8\)
+ BOOLEAN \(BOOL\)
+ CHAR \(CHARACTER\)
+ VARCHAR \(CHARACTER VARYING\)
+ DATE \(DATE data type can be used only with text, Parquet, or ORC data files, or as a partition column\)
+ TIMESTAMP
Timestamp values in text files must be in the format `yyyy-MM-dd HH:mm:ss.SSSSSS`, as the following timestamp value shows: `2017-05-01 11:30:59.000000` \.  
The length of a VARCHAR column is defined in bytes, not characters\. For example, a VARCHAR\(12\) column can contain 12 single\-byte characters or 6 two\-byte characters\. When you query an external table, results are truncated to fit the defined column size without returning an error\. For more information, see [Storage and Ranges](r_Character_types.md#r_Character_types-storage-and-ranges)   
For best performance, we recommend specifying the smallest column size that fits your data\. To find the maximum size in bytes for values in a column, use the [OCTET\_LENGTH](r_OCTET_LENGTH.md) function\. The following example returns the maximum size of values in the email column\.  

```
select max(octet_length(email)) from users;

max
---
 62
```

PARTITIONED BY \(*col\_name* *data\_type* \[, … \] \)  
A clause that defines a partitioned table with one or more partition columns\. A separate data directory is used for each specified combination, which can improve query performance in some circumstances\. Partitioned columns don't exist within the table data itself\. If you use a value for *col\_name* that is the same as a table column, you get an error\.   
After creating a partitioned table, alter the table using an [ALTER TABLE](r_ALTER_TABLE.md) … ADD PARTITION statement to add partitions\. When you add a partition, you define the location of the subfolder on Amazon S3 that contains the partition data\. You can add only one partition in each ALTER TABLE statement\.  
For example, if the table `spectrum.lineitem_part` is defined with `PARTITIONED BY (l_shipdate date)`, execute the following ALTER TABLE command to add a partition\.  

```
ALTER TABLE spectrum.lineitem_part ADD PARTITION (l_shipdate='1992-01-29') 
LOCATION 's3://spectrum-public/lineitem_partition/l_shipdate=1992-01-29';
```
To view partitions, query the [SVV\_EXTERNAL\_PARTITIONS](r_SVV_EXTERNAL_PARTITIONS.md) system view\.

ROW FORMAT DELIMITED *rowformat*  
A clause that specifies the format of the underlying data\. Possible values for *rowformat* are as follows:  
+ LINES TERMINATED BY '*delimiter*' 
+ FIELDS TERMINATED BY '*delimiter*' 
Specify a single ASCII character for '*delimiter*'\. You can specify non\-printing ASCII characters using octal, in the format `'\`*`ddd`*`'` where *`d`* is an octal digit \(0–7\) up to ‘\\177’\. The following example specifies the BEL \(bell\) character using octal\.   

```
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\007'
```
If ROW FORMAT is omitted, the default format is DELIMITED FIELDS TERMINATED BY '\\A' \(start of heading\) and LINES TERMINATED BY '\\n' \(newline\)\. 

ROW FORMAT SERDE '*serde\_name*' \[WITH SERDEPROPERTIES \( '*property\_name*' = '*property\_value*' \[, \.\.\.\] \) \]  
A clause that specifies the SERDE format for the underlying data\.     
'*serde\_name*'  
The name of the SerDe\. The following are supported:  
+ org\.apache\.hadoop\.hive\.serde2\.RegexSerDe 
+ com\.amazonaws\.glue\.serde\.GrokSerDe 
+ org\.apache\.hadoop\.hive\.serde2\.OpenCSVSerde 
+ org\.openx\.data\.jsonserde\.JsonSerDe
  + The JSON SERDE also supports Ion files\. 
  + The JSON must be well\-formed\. 
  + Timestamps in Ion and JSON must use ISO8601 format\.
  + The following SerDe property is supported for the JsonSerDe: 

    ```
    'strip.outer.array'='true' 
    ```

    Processes Ion/JSON files containing one very large array enclosed in outer brackets \( \[ … \] \) as if it contains multiple JSON records within the array\.   
WITH SERDEPROPERTIES \( '*property\_name*' = '*property\_value*' \[, \.\.\.\] \) \]  
Optionally, specify property names and values, separated by commas\.
If ROW FORMAT is omitted, the default format is DELIMITED FIELDS TERMINATED BY '\\A' \(start of heading\) and LINES TERMINATED BY '\\n' \(newline\)\. 

STORED AS *file\_format*  
The file format for data files\.   
Valid formats are as follows:  
+ PARQUET
+ RCFILE \(for data using ColumnarSerDe only, not LazyBinaryColumnarSerDe\)
+ SEQUENCEFILE
+ TEXTFILE 
+ ORC 
+ AVRO 
+ INPUTFORMAT '*input\_format\_classname*' OUTPUTFORMAT '*output\_format\_classname*' 
For INPUTFORMAT and OUTPUTFORMAT, specify a class name, as the following example shows:   

```
'org.apache.hadoop.mapred.TextInputFormat'
```

LOCATION \{ 's3://*bucket/folder*/' \| 's3://*bucket/manifest\_file*'  <a name="create-external-table-location"></a>
The path to the Amazon S3 bucket or folder that contains the data files or a manifest file that contains a list of Amazon S3 object paths\. The buckets must be in the same AWS Region as the Amazon Redshift cluster\. For a list of supported AWS Regions, see [Amazon Redshift Spectrum Considerations](c-using-spectrum.md#c-spectrum-considerations)\.  
If the path specifies a bucket or folder, for example, `'s3://mybucket/custdata/'`, Redshift Spectrum scans the files in the specified bucket or folder and any subfolders\. Redshift Spectrum ignores hidden files and files that begin with a period or underscore\.   
If the path specifies a manifest file, the `'s3://bucket/manifest_file'` argument must explicitly reference a single file—for example,`'s3://mybucket/manifest.txt'`\. It can't reference a key prefix\.   
The manifest is a text file in JSON format that lists the URL of each file that is to be loaded from Amazon S3 and the size of the file, in bytes\. The URL includes the bucket name and full object path for the file\. The files that are specified in the manifest can be in different buckets, but all the buckets must be in the same AWS Region as the Amazon Redshift cluster\. If a file is listed twice, the file is loaded twice\. The following example shows the JSON for a manifest that loads three files\.   

```
{
  "entries": [
    {"url":"s3://mybucket-alpha/custdata.1", "meta": { "content_length": 5956875 } },
    {"url":"s3://mybucket-alpha/custdata.2", "meta": { "content_length": 5997091 } },
    {"url":"s3://mybucket-beta/custdata.1", "meta": { "content_length": 5978675 } }
  ]
}
```
To reference files created using UNLOAD, you can use the manifest created using [UNLOAD](r_UNLOAD.md) with the MANIFEST parameter\. The manifest file is compatible with a manifest file for [COPY from Amazon S3](copy-parameters-data-source-s3.md), but uses different keys\. Keys that aren't used are ignored\. 

TABLE PROPERTIES \( '*property\_name*'='*property\_value*' \[, \.\.\.\] \)   
A clause that sets the table definition for table properties\.   
Table properties are case\-sensitive\.  
 'compression\_type'='*value*'   
 A property that sets the type of compression to use if the file name does not contain an extension\. If you set this property and there is a file extension, the extension is ignored and the value set by the property is used\. Valid values for compression type are as follows:  
+ bzip2
+ gzip
+ none
+ snappy  
'numRows'='*row\_count*'   
A property that sets the numRows value for the table definition\. To explicitly update an external table's statistics, set the numRows property to indicate the size of the table\. Amazon Redshift doesn't analyze external tables to generate the table statistics that the query optimizer uses to generate a query plan\. If table statistics are not set for an external table, Amazon Redshift generates a query execution plan based on an assumption that external tables are the larger tables and local tables are the smaller tables\.  
'skip\.header\.line\.count'='*line\_count*'  
A property that sets number of rows to skip at the beginning of each source file\.  
'serialization\.null\.format'=' '  
A property that specifies Spectrum should return a `NULL` value when there is an exact match with the text supplied in a field\.  
'orc\.schema\.resolution'='mapping\_type'  
A property that sets the column mapping type for tables that use ORC data format\. This property is ignored for other data formats\.  
Valid values for column mapping type are as follows:   
+ name 
+ position 
If the *orc\.schema\.resolution* property is omitted, columns are mapped by name by default\. If *orc\.schema\.resolution* is set to any value other than *'name'* or *'position'*, columns are mapped by position\. For more information about column mapping, see [Mapping External Table Columns to ORC Columns](c-spectrum-external-tables.md#c-spectrum-column-mapping-orc)  
The COPY command maps to ORC data files only by position\. The *orc\.schema\.resolution* table property has no effect on COPY command behavior\. 

## Usage Notes<a name="r_CREATE_EXTERNAL_TABLE_usage"></a>

You can't view details for Amazon Redshift Spectrum tables using the same resources you use for standard Amazon Redshift tables, such as [PG\_TABLE\_DEF](r_PG_TABLE_DEF.md), [STV\_TBL\_PERM](r_STV_TBL_PERM.md), PG\_CLASS, or information\_schema\. If your business intelligence or analytics tool doesn't recognize Redshift Spectrum external tables, configure your application to query [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md)\.

### Permissions to Create and Query External Tables<a name="r_CREATE_EXTERNAL_TABLE_usage-permissions"></a>

To create external tables, you must be the owner of the external schema or a superuser\. To transfer ownership of an external schema, use [ALTER SCHEMA](r_ALTER_SCHEMA.md)\. The following example changes the owner of the `spectrum_schema` schema to `newowner`\.

```
alter schema spectrum_schema owner to newowner;
```

To run a Redshift Spectrum query, you need the following permissions:
+ Usage permission on the schema 
+ Permission to create temporary tables in the current database 

The following example grants usage permission on the schema `spectrum_schema` to the `spectrumusers` user group\.

```
grant usage on schema spectrum_schema to group spectrumusers;
```

The following example grants temporary permission on the database `spectrumdb` to the `spectrumusers` user group\. 

```
grant temp on database spectrumdb to group spectrumusers;
```

### Pseudocolumns<a name="r_CREATE_EXTERNAL_TABLE_usage-pseudocolumns"></a>

By default, Amazon Redshift creates external tables with the pseudocolumns *$path* and *$size*\. Select these columns to view the path to the data files on Amazon S3 and the size of the data files for each row returned by a query\. The *$path* and *$size* column names must be delimited with double quotation marks\. A *SELECT \** clause doesn't return the pseudocolumns \. You must explicitly include the *$path* and *$size* column names in your query, as the following example shows\.

```
select "$path", "$size"
from spectrum.sales_part
where saledate = '2008-12-01';
```

You can disable creation of pseudocolumns for a session by setting the *spectrum\_enable\_pseudo\_columns* configuration parameter to *false*\. 

**Important**  
Selecting *$size* or *$path* incurs charges because Redshift Spectrum scans the data files on Amazon S3 to determine the size of the result set\. For more information, see [Amazon Redshift Pricing](https://aws.amazon.com/redshift/pricing/)\.

## Examples<a name="r_CREATE_EXTERNAL_TABLE_examples"></a>

The following example creates a table named SALES in the Amazon Redshift external schema named `spectrum`\. The data is in tab\-delimited text files\. The TABLE PROPERTIES clause sets the numRows property to 170,000 rows\.

```
create external table spectrum.sales(
salesid integer,
listid integer,
sellerid integer,
buyerid integer,
eventid integer,
saledate date,
qtysold smallint,
pricepaid decimal(8,2),
commission decimal(8,2),
saletime timestamp)
row format delimited
fields terminated by '\t'
stored as textfile
location 's3://awssampledbuswest2/tickit/spectrum/sales/'
table properties ('numRows'='170000');
```

The following example creates a table that uses the JsonSerDe to reference data in JSON format\.

```
create external table spectrum.cloudtrail_json (
event_version int
event_id bigint,
event_time timestamp,
event_type varchar(10),
awsregion varchar(20),
event_name varchar(max),
event_source varchar(max),
requesttime timestamp,
useragent varchar(max),
recipientaccountid bigint) 
row format serde 'org.openx.data.jsonserde.JsonSerDe'
with serdeproperties (
'dots.in.keys' = 'true',
'mapping.requesttime' = 'requesttimestamp'
) location 's3://mybucket/json/cloudtrail';
```

For a list of existing databases in the external data catalog, query the [SVV\_EXTERNAL\_DATABASES](r_SVV_EXTERNAL_DATABASES.md) system view\. 

```
select eskind,databasename,esoptions from svv_external_databases order by databasename;
```

```
eskind | databasename | esoptions                                                                        
-------+--------------+----------------------------------------------------------------------------------
     1 | default      | {"REGION":"us-west-2","IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
     1 | sampledb     | {"REGION":"us-west-2","IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
     1 | spectrumdb   | {"REGION":"us-west-2","IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
```

To view details of external tables, query the [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md) system views\.

The following example queries the SVV\_EXTERNAL\_TABLES view\.

```
select schemaname, tablename, location from svv_external_tables;
```

```
schemaname | tablename            | location                                               
-----------+----------------------+--------------------------------------------------------
spectrum   | sales                | s3://awssampledbuswest2/tickit/spectrum/sales          
spectrum   | sales_part           | s3://awssampledbuswest2/tickit/spectrum/sales_partition
```

The following example queries the SVV\_EXTERNAL\_COLUMNS view\. 

```
select * from svv_external_columns where schemaname like 'spectrum%' and tablename ='sales';
```

```
schemaname | tablename | columnname | external_type | columnnum | part_key
-----------+-----------+------------+---------------+-----------+---------
spectrum   | sales     | salesid    | int           |         1 |        0
spectrum   | sales     | listid     | int           |         2 |        0
spectrum   | sales     | sellerid   | int           |         3 |        0
spectrum   | sales     | buyerid    | int           |         4 |        0
spectrum   | sales     | eventid    | int           |         5 |        0
spectrum   | sales     | saledate   | date          |         6 |        0
spectrum   | sales     | qtysold    | smallint      |         7 |        0
spectrum   | sales     | pricepaid  | decimal(8,2)  |         8 |        0
spectrum   | sales     | commission | decimal(8,2)  |         9 |        0
spectrum   | sales     | saletime   | timestamp     |        10 |        0
```

To view table partitions, use the following query\.

```
select schemaname, tablename, values, location 
from svv_external_partitions
where tablename = 'sales_part';
```

```
schemaname | tablename  | values         | location                                                                
-----------+------------+----------------+-------------------------------------------------------------------------
spectrum   | sales_part | ["2008-01-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01
spectrum   | sales_part | ["2008-02-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02
spectrum   | sales_part | ["2008-03-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03
spectrum   | sales_part | ["2008-04-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04
spectrum   | sales_part | ["2008-05-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05
spectrum   | sales_part | ["2008-06-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06
spectrum   | sales_part | ["2008-07-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07
spectrum   | sales_part | ["2008-08-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08
spectrum   | sales_part | ["2008-09-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09
spectrum   | sales_part | ["2008-10-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10
spectrum   | sales_part | ["2008-11-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11
spectrum   | sales_part | ["2008-12-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12
```

The following example returns the total size of related data files for an external table\.

```
select distinct "$path", "$size"
   from spectrum.sales_part;

 $path                                 | $size
---------------------------------------+-------
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/ |  1616
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/ |  1444
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/ |  1444
```

## Partitioning Examples<a name="r_CREATE_EXTERNAL_TABLE_examples-partitioning"></a>

To create an external table partitioned by date, run the following command\.

```
create external table spectrum.sales_part(
salesid integer,
listid integer,
sellerid integer,
buyerid integer,
eventid integer,
dateid smallint,
qtysold smallint,
pricepaid decimal(8,2),
commission decimal(8,2),
saletime timestamp)
partitioned by (saledate date)
row format delimited
fields terminated by '|'
stored as textfile
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/'
table properties ('numRows'='170000');
```

To add the partitions, run the following ALTER TABLE commands\.

```
alter table spectrum.sales_part
add if not exists partition (saledate='2008-01-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-02-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-03-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-04-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-05-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-06-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-07-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-08-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-09-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-10-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-11-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-12-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12/';
```

To select data from the partitioned table, run the following query\.

```
select top 10 spectrum.sales_part.eventid, sum(spectrum.sales_part.pricepaid) 
from spectrum.sales_part, event
where spectrum.sales_part.eventid = event.eventid
  and spectrum.sales_part.pricepaid > 30
  and saledate = '2008-12-01'
group by spectrum.sales_part.eventid
order by 2 desc;
```

```
eventid | sum     
--------+---------
    914 | 36173.00
   5478 | 27303.00
   5061 | 26383.00
   4406 | 26252.00
   5324 | 24015.00
   1829 | 23911.00
   3601 | 23616.00
   3665 | 23214.00
   6069 | 22869.00
   5638 | 22551.00
```

To view external table partitions, query the [SVV\_EXTERNAL\_PARTITIONS](r_SVV_EXTERNAL_PARTITIONS.md) system view\.

```
select schemaname, tablename, values, location from svv_external_partitions
where tablename = 'sales_part';
```

```
schemaname | tablename  | values         | location                                         
-----------+------------+----------------+--------------------------------------------------
spectrum   | sales_part | ["2008-01-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01
spectrum   | sales_part | ["2008-02-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02
spectrum   | sales_part | ["2008-03-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03
spectrum   | sales_part | ["2008-04-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04
spectrum   | sales_part | ["2008-05-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05
spectrum   | sales_part | ["2008-06-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06
spectrum   | sales_part | ["2008-07-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07
spectrum   | sales_part | ["2008-08-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08
spectrum   | sales_part | ["2008-09-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09
spectrum   | sales_part | ["2008-10-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10
spectrum   | sales_part | ["2008-11-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11
spectrum   | sales_part | ["2008-12-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12
```

## Row Format Examples<a name="r_CREATE_EXTERNAL_TABLE_examples-row-format"></a>

The following shows an example of specifying the ROW FORMAT SERDE parameters for data files stored in AVRO format\.

```
create external table spectrum.sales(salesid int, listid int, sellerid int, buyerid int, eventid int, dateid int, qtysold int, pricepaid decimal(8,2), comment VARCHAR(255))
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.avro.AvroSerDe'
WITH SERDEPROPERTIES ('avro.schema.literal'='{\"namespace\": \"dory.sample\",\"name\": \"dory_avro\",\"type\": \"record\", \"fields\": [{\"name\":\"salesid\", \"type\":\"int\"}, 
{\"name\":\"listid\", \"type\":\"int\"}, 
{\"name\":\"sellerid\", \"type\":\"int\"}, 
{\"name\":\"buyerid\", \"type\":\"int\"}, 
{\"name\":\"eventid\",\"type\":\"int\"}, 
{\"name\":\"dateid\",\"type\":\"int\"}, 
{\"name\":\"qtysold\",\"type\":\"int\"}, 
{\"name\":\"pricepaid\", \"type\": {\"type\": \"bytes\", \"logicalType\": \"decimal\", \"precision\": 8, \"scale\": 2}}, {\"name\":\"comment\",\"type\":\"string\"}]}')
STORED AS AVRO
location 's3://mybucket/avro/sales' ;
```

The following shows an example of specifying the ROW FORMAT SERDE parameters using RegEx\.

```
create external table spectrum.types(
cbigint bigint,
cbigint_null bigint,
cint int,
cint_null int)
row format serde 'org.apache.hadoop.hive.serde2.RegexSerDe'
with serdeproperties ('input.regex'='([^\\x01]+)\\x01([^\\x01]+)\\x01([^\\x01]+)\\x01([^\\x01]+)')
stored as textfile
location 's3://mybucket/regex/types';
```

The following shows an example of specifying the ROW FORMAT SERDE parameters using Grok\.

```
create external table spectrum.grok_log(
timestamp varchar(255),
pid varchar(255),
loglevel varchar(255),
progname varchar(255),
message varchar(255))
row format serde 'com.amazonaws.glue.serde.GrokSerDe'
with serdeproperties ('input.format'='[DFEWI], \\[%{TIMESTAMP_ISO8601:timestamp} #%{POSINT:pid:int}\\] *(?<loglevel>:DEBUG|FATAL|ERROR|WARN|INFO) -- +%{DATA:progname}: %{GREEDYDATA:message}')
stored as textfile
location 's3://mybucket/grok/logs';
```