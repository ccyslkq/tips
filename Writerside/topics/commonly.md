# 常用命令

## 服务器连接
- Jumpserver : `ssh yang.lingshu@10.21.200.56 -p2222 -i C:\Users\yang.lingshu\.ssh\id_rsa`
- 华为云 : `ssh yang.lingshu@121.36.169.229 -p2222 -i C:\Users\yang.lingshu\.ssh\id_rsa`
- 45测服 : `ssh yang.lingshu@10.21.200.45 -p22 -i C:\Users\yang.lingshu\.ssh\id_rsa`


## 常用SQL
### 正在执行
```SQL
select * from information_schema.`PROCESSLIST` where info is not null order by time desc \G;
```  

### 查找sleep进程
```SQL
SELECT group_concat(concat("kill ", ID) SEPARATOR ';') FROM information_schema.`PROCESSLIST` t WHERE t.`COMMAND` IN ('Sleep');
```

### 输出SQL换行
`nopager`
### tokudb热加索引
```SQL
SET tokudb_create_index_online=on;
CREATE INDEX idx_date_mid ON rank_for_calculate (`date`,`mid`);
```
改动mysql配置my.cnf后，如果不想重启进程，可以set global xxx手动修改当前进程配置。

## mysqldump
```Shell
mysqldump --skip-lock-tables=TRUE --add-locks=FALSE --no-create-info=TRUE --no-create-db=TRUE --lock-tables=FALSE -h10.21.200.32 -P3306 -um_reader -preaderqweasd --databases industry --tables industry_solidify_item --where='mid=3004 and date >= 20220901' | mysql -h10.21.200.110 -uindustry_test -pindustry20210719 industry
```

## mysql用户管理
- 创建用户：  `create user username@host  identified by 'password'`
- 查看权限：  `show grants for username@host`
- 分配权限：  `grant privileges on db.table to user@host`
- 查看用户：  `select host,user from mysql.user where user = ''`


## Docker操作
- 运行的docker：  `docker ps`
- 进入docker：   `docker exec -it <container-id> bash`
- 修改配置后测试：    `docker exec -it <container-id> nginx -t`
- 重启docker：   `docker restart <container-id>`
- 查看目录映射：     `docker inspect -f '{{ .Mounts }}'  <container-id>`

## 批量kill进程
`ps -ef | grep 'xxx' | grep -v grep | grep -v /bin/sh | awk '{print $2}' | xargs kill`

`awk '{print $2}'` 表示筛选出我们所关注的进程号，`$2` 表示每行第二个变量，在这个例子中`ps -ef`输出的第二个变量就是进程号。  
`xargs kill` 将`xargs`前面命令的输出结果作为`kill`命令的参数，即`kill`进程号


## RocksDB直写模式
解决一次更新行数超过锁表限制
```SQL
SET session rocksdb_bulk_load=1;
...
SET session rocksdb_bulk_load=0;
```

## 检查系统cron服务状态
### systemd
```Shell
sudo systemctl status crond
sudo systemctl restart crond
```
### 其他
```Shell
sudo service crond status
sudo service crond restart
```

## linux软链接
- 创建软链接： `ln -s [源文件/目录] [目标文件/目录]`  
- 修改：`ln -snf [源] [目标]`  
```Shell
ln -s /var/resource /var/new_dir    # /var/new_dir  ->  /var/resource
```

- 删除 `unlink [目标]`
```Shell
unlink /var/new_dir
```
- 不建议使用`rm`命令删除，`rm -rf /var/new_dir/` 会导致源文件（目录）`/var/resource/` 被完全删除
- 非要用 `rm` 删除的话  `rm /var/new_dir`   （不要使用r,f  参数，也不要在末尾加斜杠。因为软链接不是目录）
> [如何正确的删除软链接](https://fantiq.github.io/2017/07/06/%E5%A6%82%E4%BD%95%E6%AD%A3%E7%A1%AE%E7%9A%84%E5%88%A0%E9%99%A4%E8%BD%AF%E8%BF%9E%E6%8E%A5/)
  

