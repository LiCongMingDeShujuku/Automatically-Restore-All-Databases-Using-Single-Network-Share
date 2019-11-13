![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 使用单个网络共享自动还原所有数据库
#### Automatically Restore All Databases Using Single Network Share
**发布-日期: 2014年05月29日 (评论)**


## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
你可能有一个包含所有数据库备份文件的网络存储位置。有一天，你需要将这些数据库（或恢复数据库）放在新服务器上，以便你可以正确地停用旧服务器。这里有一些SQL 逻辑会自动为你执行此操作。无论你拥有多少数据库备份，都可以通过此逻辑实现。


## English
You probably have a network storage location with all your Database Backup files in it. One day you’ll need to place those databases ( or restore the databases ) on a new server so you old server can be properly decommissioned. Here’s some SQL Logic that will automatically do that for you. Doesn’t matter how many database backups you have; this will do them all.

---
## Logic
```SQL

use master;
set nocount on
 
declare @create_restore_logic varchar(max) set @create_restore_logic = '' select @create_restore_logic = @create_restore_logic + '
use master;
set nocount on
go
declare @database_name varchar (255) declare @backup_file_name varchar (255) set @database_name = ''' + replace(name, '''', '''''') + '''
set @backup_file_name = ''\\MyNetworkShareLocation\' + replace(name, '''', '') + '.bak'' declare @filelistonly table
(
logicalname nvarchar (128) , physicalname nvarchar (260) , [type] char (1)
, filegroupname nvarchar (128) , size numeric (20,0) , maxsize numeric (20,0) , fileid bigint
, createlsn numeric (25,0) , droplsn numeric (25,0) , uniqueid uniqueidentifier
, readonlylsn numeric (25,0) , readwritelsn numeric (25,0) , backupsizeinbytes bigint
, sourceblocksize int
, filegroupid int
, loggroupguid uniqueidentifier
, differentialbaselsn numeric (25,0) , differentialbaseguid uniqueidentifier
, isreadonl bit
, ispresent bit
, tdethumbprint varbinary (32)
)
insert into
@filelistonly exec (''restore filelistonly from disk = '''''' + @backup_file_name + '''''''') declare @restore_line0 varchar (255) declare @restore_line1 varchar (255) declare @restore_line2 varchar (255) declare @stats varchar (255) declare @move_files varchar (max) set @restore_line0 = (''use master; '')
set @restore_line1 = (''exec master..sp_killallprocessindb '''''' + @database_name + '''''';'')
set @restore_line2 = (select ''restore database ['' + @database_name + ''] from disk = '''''' + @backup_file_name + '''''' with replace, recovery, '') set @stats = (''stats = 20;'')
set @move_files = ''''
select @move_files = @move_files + ''move '''''' + logicalname + '''''' to '''''' + physicalname + '''''','' + char(10) from @filelistonly order by fileid asc
 
select/**/ -- replace this line with: exec   --替换这行为：
(
@restore_line0
+ @restore_line1
+ @restore_line2
+ @move_files
+ @stats
)
go
'
from sys.databases where name not in ('master', 'model', 'tempdb', 'msdb') order by name asc
 
select (@create_restore_logic) for xml path (''), type

```
注意最后一行的“for xml path”将在management studio中为你提供另一个选项卡（xml选项卡），以便你可以完整地查看其中的所有输出。如果你满意的话，可以将“select”改为“exec”，或者将整个xml输出粘贴到另一个管理工作室窗口，并监控整个过程。
希望以上信息可以帮助到你。

---

Notice the last line “for xml path”… This will give you another tab ( xml tab ) in management studio so you can see ALL the output in it’s entirety. If satisfactory stimply change the laste ‘select’ to an ‘exec’, or you can paste the entire xml output into another management studio window, and babysit the whole process.
Hope it’s helpful.

--- 


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

