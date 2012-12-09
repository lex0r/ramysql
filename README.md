ramysql
=======

Utility script for Ubuntu/Linux that moves MySQL to ramfs.

Disclaimer: currently the script is well tested under Ubuntu 12.04 but should work on any older Ubuntu box.
Always make backups of critical databases!

Features:
- move all or certain MySQL databases to ramfs file system (and back to disk), i.e store them in memory (2-8 time performance improvement!)
- automatically creates mount point, configures apparmor and on-shutdown/on-reboot scripts that will save RAM back to disk
- possibility to periodically flush data back to disk (in order to protect from OS glitches)

Known issues:
Flush operation is dangerous when run during DB operations! Fix is trivial (stop and then start MySQL) and will be available soon.

Pre-requisites
-----
MySQL must be configured using the following parameters in my.cnf (if you are using InnoDB):
```
[mysqld]
...
innodb_file_per_table
innodb_flush_method=O_DIRECT
```
Also, setting `innodb_buffer_pool_size` to a higher than default value can speed your MySQL

Usage
----------
1. Configuration:
The main concern - SIZE variable. Set to a reasonable value.

2. Run (provided the script is in /usr/local/bin):
m2m ramify "database1,database2,database3"
MySQL will be reconfigured and moved to RAM

3. Restore:
m2m restore

4. Flush (do not restore but only update disk-stored MySQL data)
m2m flush

Notes:
Database name is allowed to contain only letters, numbers, "_", "-".