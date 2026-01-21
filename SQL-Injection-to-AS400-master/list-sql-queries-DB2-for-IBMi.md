# Standard Queries for DB2 for IBM i

### Version
```sql
SELECT os_name||' '||os_version||'.'||os_release FROM sysibmadm.env_sys_info
```
### Comments
```sql
SELECT blah FROM foo -- comment like this (double dash)
```

### Current User / Current Database
```sql
SELECT user FROM sysibm.sysdummy1
```

### List Databases
```sql
SELECT DISTINCT table_schema FROM QSYS2.SYSTABLES
```

### List Tables
```sql
SELECT system_table_name, table_schema FROM QSYS2.SYSTABLES WHERE table_type='T'
```

### List Columns with short table names
```sql
SELECT sqlcolumns.sys_cname, systables.system_table_name, SUBSTR(sqlcolumns.type_name, 1, 20) FROM QSYS2.SYSTABLES as systables 
JOIN SYSIBM.SQLCOLUMNS as sqlcolumns 
ON (systables.table_name = sqlcolumns.table_name AND systables.table_schema=sqlcolumns.table_schem)
```

### Find Tables From Column Name
```sql
SELECT systables.system_table_name, sqlcolumns.sys_cname FROM QSYS2.SYSTABLES as systables 
JOIN SYSIBM.SQLCOLUMNS as sqlcolumns 
ON (systables.table_name = sqlcolumns.table_name AND systables.table_schema=sqlcolumns.table_schem)
WHERE sqlcolumns.name LIKE '%password%'
```

### Select Nth Row
```sql
SELECT DISTINCT system_table_name FROM (SELECT ROW_NUMBER() OVER () AS CAP,table_schema FROM QSYS2.SYSTABLES) AS qq WHERE CAP=42
```

### Select Nth Char
```sql
SELECT SUBSTR(system_table_name, 42, 1) FROM QSYS2.SYSTABLES
```

### Bitwise AND/OR/NOT/XOR
```sql
SELECT bitand(1,0) FROM sysibm.sysdummy1 -- returns 0. Also available bitandnot, bitor, bitxor, bitnot
```

### ASCII Value
```sql
SELECT chr(65) FROM sysibm.sysdummy1 -- returns 'A'
```

### Char -> ASCII Value
```sql
SELECT ascii('A') FROM sysibm.sysdummy1 -- returns 65
```

### Casting
```sql
SELECT cast('42' AS integer) FROM sysibm.sysdummy1
SELECT cast(42 AS char) FROM sysibm.sysdummy1

SELECT CHAR (42) FROM sysibm.sysdummy1;

SELECT try_cast('42' AS integer) FROM sysibm.sysdummy1;
```

### String Concat
```sql
SELECT CONCAT('a','b') FROM sysibm.sysdummy1 -- returns 'ab'
SELECT 'a' || 'b' CONCAT 'c' FROM sysibm.sysdummy1 -- returns 'abc'
```

### Case Statement
```sql
SELECT CASE WHEN (1=1) THEN 'A' ELSE 'B' END from sysibm.sysdummy1
```

### Avoiding Quotes
```sql
SELECT chr(73)||chr(84)||chr(82) FROM sysibm.sysdummy1 -- returns "ITR"
```

### Hostname and OS Info
```sql
SELECT os_name,os_version,os_release,host_name FROM sysibmadm.env_sys_info
```

### IP Info
```sql
SELECT sysibm.client_host, sysibm.client_ipaddr FROM sysibm.sysdummy1
```

# Avenues for Further Exploration

### Authorizations
```sql
SELECT * FROM QSYS2.AUTHORIZATION_LIST_USER_INFO WHERE AUTHORIZATION_NAME='<user>';

SELECT * FROM QSYS2.AUTHORIZATION_LIST_USER_INFO WHERE AUTHORIZATION_NAME='*PUBLIC';
```

### Command Execution
The iSeries execution context is particular and does not allow the use of commands valid on Linux. The following query seems to work if the user has sufficient rights and allows reading the profile information of a user.
```sql
SELECT VARCHAR(QSYS2.QCMDEXC('DSPUSRPRF USRPRF(IBM)')) FROM sysibm.sysdummy1
```
However, it returns no information in DB2 and should be used with an output file to retrieve its result.

### File Reading
```sql
SELECT * FROM TABLE(QSYS2.IFS_READ(PATH_NAME => '/path/to/file', END_OF_LINE => 'CRLF'));
```

### System Data
```sql
SELECT ENVIRONMENT_VARIABLE_NAME, ENVIRONMENT_VARIABLE_VALUE FROM QSYS2.ENVIRONMENT_VARIABLE_INFO WHERE ENVIRONMENT_VARIABLE_TYPE = 'SYSTEM'

SELECT DATA_BINARY FROM TABLE(QSYS2.USER_SPACE(USER_SPACE => 'USRSPACE1', USER_SPACE_LIBRARY => '*CURLIB*'));

SELECT DATA_BINARY FROM TABLE(QSYS2.USER_SPACE(USER_SPACE => 'USRSPACE1', USER_SPACE_LIBRARY => '*LIBL*'));
```
