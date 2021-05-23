# 常用口令
```shell
$ systemctl enable firewalld.service
$ systemctl disable firewalld.service
$ systemctl start firewalld
$ systemctl stop firewalld
$ systemctl status firewalld

$ firewall-cmd --state
$ firewall-cmd --reload
$ firewall-cmd --list-ports
$ firewall-cmd --zone=public --add-port=9200/tcp --permanent
$ firewall-cmd --zone=public --remove-port=9200/tcp --permanent
```

转自 https://www.cnblogs.com/marso/archive/2018/01/06/8214927.html