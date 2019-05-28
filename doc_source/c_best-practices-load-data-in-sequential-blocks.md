# Load Data in Sequential Blocks<a name="c_best-practices-load-data-in-sequential-blocks"></a>

If you need to add a large quantity of data, load the data in sequential blocks according to sort order to eliminate the need to vacuum\. 

For example, suppose that you need to load a table with events from January 2017 to December 2017\. Load the rows for January, then February, and so on\. Your table is completely sorted when your load completes, and you don't need to run a vacuum\. For more information, see [Use Time\-Series Tables](c_best-practices-time-series-tables.md)\.

When loading very large datasets, the space required to sort might exceed the total available space\. By loading data in smaller blocks, you use much less intermediate sort space during each load\. In addition, loading smaller blocks make it easier to restart if the COPY fails and is rolled back\. 