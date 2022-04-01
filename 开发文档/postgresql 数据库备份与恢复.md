## postgresql 数据库备份与恢复
<p align='center'>2022-04-01</p>

[TOC]
### 1. 数据库备份命令
```
pg_dump -Fc --no-owner -U <db user> -d <db name> <backup file>;
例：
pg_dump -Fc --no-owner -U debug -d ces > /ces0819.dmp
"debug"：连接数据库用户
"ces":需要备份的数据库
"/ces0819.dmp":数据库备份文件路径
参数说明：
"-Fc"：指定备份文件格式，使用pg_restore恢复数据
"--no-owner":不指定用户
"-U"：数据库用户
"-h":数据库主机ip
"-p":数据库连接端口
```
### 2. 数据库备份恢复
```
pg_restore -c -U <db user> --no-owner --role=<role name> -d <db name> <backup file>;
例：
pg_restore -c -U debug --no-owner --role=debug -d t1 /ces0819.dmp
将/ces0819.dmp备份文件恢复至t1数据库，owner为debug
```
### 3. 数据库备份脚本db_backup.sh
```
#!/bin/bash
# 备份存放位置--根据实际项目情况修改
BACKUP_DIR=/app/backup/
# 如果不存在就创建
if [ ! -d "$BACKUP_DIR" ]; then
mkdir -p "$BACKUP_DIR"
fi
cd $BACKUP_DIR
# 删除7天之前的备份--根据实际项目情况修改
deleteDIR=$(date --date='7 day ago' +%Y-%m-%d)
rm -rf $deleteDIR
# 假设每天凌晨执行 备份数据文件夹名为昨日 yyyy-MM-dd 格式
dateDIR=$(date --date='1 day ago' +%Y-%m-%d)
mkdir $dateDIR
# 备份指定的数据库--根据实际项目情况修改
backupDB=(db_name0 db_name1 db_name2)
backupDBArr=$(echo ${backupDB[@]})
# 使用压缩转储每一个数据库
for i in $backupDBArr; do
pg_dump -U postgres $i | gzip &gt; $BACKUP_DIR$dateDIR/${i}_${dateDIR}.gz
done
```
### 4. crontab 增加定时备份任务--根据实际项目情况修改
```
01 00 * * * sh /.../db_backup.sh
```