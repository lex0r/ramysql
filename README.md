# ramysql

Utility script for Ubuntu/Linux that moves MySQL to ramfs.

**Disclaimer: currently the script is well tested under Ubuntu 12.04 but should work on any older Ubuntu box. Always make backups of critical databases!**

Features:
- move all or certain MySQL databases to ramfs file system (and back to disk), i.e store them in memory (2-8 time performance improvement!)
- automatically creates mount point, configures apparmor and on-shutdown/on-reboot scripts that will save RAM back to disk
- possibility to manually flush data back to disk (in order to protect from OS glitches)

## Pre-requisites
The main one: you **don't have a quick SSD**, but your good old HDD is way too slow... :)
Otherwise, you don't actually need this utility because SSD speed is more or less comparable with RAM storage.

MySQL must be configured using the following parameters in my.cnf (if you are using InnoDB):
```
[mysqld]
...
innodb_file_per_table
innodb_flush_method=O_DIRECT
```
However, this will only take effect if you FIRST do the following:
- Dump all of the databases (except for mysql, information/performance_ schema)
- Stop MySQL
- Delete ibdata1, ib_logfile0, ib_logfile1 files from MySQL datadir (they will be recreated)
- Change the config by adding line from above
- Start MySQL

Also, setting `innodb_buffer_pool_size` to a higher than default value can speed your MySQL noticeably.
_Phpmyadmin notes: using it for export of huge amount of data is not recommended (due to PHP timeout), use mysqldump instead. Also, don't export its own database to overall dump file since it is MyISAM_

## Usage

1. Configuration:
The main concern - SIZE variable. Set to a reasonable value.

2. Run (provided the script is in /usr/local/bin):
m2m ramify "database1,database2,database3"
MySQL will be reconfigured and moved to RAM

3. Restore:
m2m restore

4. Flush (do not restore but only update disk-stored MySQL data)
m2m flush
Tip: you could set up a cron job to flush your data automatically. Current implementation, however, will simply
drop any running operations without letting you choose if you are ok with the interruption for flush.

_Notes: Database name is allowed to contain only letters, numbers, underscores, hyphens and dots._
