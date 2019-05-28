# Managing Database Security<a name="r_Database_objects"></a>

**Topics**
+ [Amazon Redshift Security Overview](c_security-overview.md)
+ [Default Database User Privileges](r_Privileges.md)
+ [superuser](r_superusers.md)
+ [Users](r_Users.md)
+ [Groups](r_Groups.md)
+ [Schemas](r_Schemas_and_tables.md)
+ [Example for Controlling User and Group Access](t_user_group_examples.md)

You manage database security by controlling which users have access to which database objects\.

Access to database objects depends on the privileges that you grant to user accounts or groups\. The following guidelines summarize how database security works:
+ By default, privileges are granted only to the object owner\.
+ Amazon Redshift database users are named user accounts that can connect to a database\. A user account is granted privileges explicitly, by having those privileges assigned directly to the account, or implicitly, by being a member of a group that is granted privileges\.
+ Groups are collections of users that can be collectively assigned privileges for easier security maintenance\.
+ Schemas are collections of database tables and other database objects\. Schemas are similar to operating system directories, except that schemas cannot be nested\. Users can be granted access to a single schema or to multiple schemas\.

For examples of security implementation, see [Example for Controlling User and Group Access](t_user_group_examples.md)\.