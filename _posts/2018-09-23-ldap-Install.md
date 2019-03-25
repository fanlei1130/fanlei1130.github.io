---
layout:     post
title:      "OpenLDAP 安装教程"
subtitle:   ""
date:       2018-09-23 12:00:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - OpenLDAP
    - 权限验证
    - 安装教程
---

# LDAP 安装教程

在centos7上安装OpenLDAP

## 环境准备

两台虚拟机

node01 IP：192.168.1.143 server端

node02 IP：192.168.1.146 client端

均关闭iptables和selinux和firewall

## Service端

步骤：

1. 安装包
2. 拷贝DB_CONFIG文件
3. 设置目录权限
4. 创建LDAP管理员密码
5. 修改配置文件(三个)
6. 启动并设置开机启动slapd服务
7. 导入基本Schema
8. 导入base.ldif文件
9. 配置migrationtools
10. 导入系统用户和组
11. 重启服务

### 安装包

```bash
yum install openldap-servers openldap-clients migrationtools
```

### 拷贝 DB_CONFIG 文件

```bash
cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
```

### 设置目录权限

```bash
chown -R ldap. /var/lib/ldap/
```

### 创建 LDAP 管理员密码

```bash
slappasswd
```
#### 输入两次后，保存密文

```bash
New password:
Re-enter new password:
{SSHA}AFU2R+sLzJgjUIoW1B5SxcTUdFcuncLz
```

### 修改配置文件(三个)

```bash
vim /etc/openldap/slapd.d/cn\=config/olcDatabase\=\{0\}config.ldif
```

```vim
# AUTO-GENERATED FILE - DO NOT EDIT!! Use ldapmodify.
# CRC32 d30fb98e
dn: olcDatabase={0}config
objectClass: olcDatabaseConfig
olcDatabase: {0}config
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=extern
 al,cn=auth" manage by * none
structuralObjectClass: olcDatabaseConfig
entryUUID: 73e7786c-50fa-1038-9bfb-9bfcf0927062
creatorsName: cn=config
createTimestamp: 20180920082518Z
entryCSN: 20180920082518.739228Z#000000#000#000000
modifiersName: cn=config
modifyTimestamp: 20180920082518Z
olcRootPW: {SSHA}37kYCk8iLCmIrGnRvLc7XLAuPqftFUF/        # 添加该行（密码）
```

```bash
vim /etc/openldap/slapd.d/cn\=config/olcDatabase\=\{2\}hdb.ldif
```


```vim
# AUTO-GENERATED FILE - DO NOT EDIT!! Use ldapmodify.
# CRC32 d41d7411
dn: olcDatabase={2}hdb
objectClass: olcDatabaseConfig
objectClass: olcHdbConfig
olcDatabase: {2}hdb
olcDbDirectory: /var/lib/ldap
olcSuffix: dc=sitoi,dc=cn                                       # 更改dc
olcRootDN: cn=Manager,dc=sitoi,dc=cn                            # 更改dc
olcRootPW: {SSHA}37kYCk8iLCmIrGnRvLc7XLAuPqftFUF/               # 添加该行（密码）
olcDbIndex: objectClass eq,pres
olcDbIndex: ou,cn,mail,surname,givenname eq,pres,sub
structuralObjectClass: olcHdbConfig
entryUUID: 73e77fe2-50fa-1038-9bfd-9bfcf0927062
creatorsName: cn=config
createTimestamp: 20180920082518Z
entryCSN: 20180920082518.739419Z#000000#000#000000
modifiersName: cn=config
modifyTimestamp: 20180920082518Z
olcAccess: {0}to attrs=userPassword by self write by dn.base="cn=Manager,dc=sitoi,dc=cn" write by anonymous auth by * none   # 添加该行
olcAccess: {1}to * by dn.base="cn=Manager,dc=sitoi,dc=cn" write by self write by * read                                      # 添加该行
```


```bash
vim /etc/openldap/slapd.d/cn\=config/olcDatabase\=\{1\}monitor.ldif
```


```vim
# AUTO-GENERATED FILE - DO NOT EDIT!! Use ldapmodify.
# CRC32 261d1986
dn: olcDatabase={1}monitor
objectClass: olcDatabaseConfig
olcDatabase: {1}monitor
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=extern
 al,cn=auth" read by dn.base="cn=Manager,dc=sitoi,dc=cn" read by * none    # 修改dc信息
structuralObjectClass: olcDatabaseConfig
entryUUID: 73e77bbe-50fa-1038-9bfc-9bfcf0927062
creatorsName: cn=config
createTimestamp: 20180920082518Z
entryCSN: 20180920082518.739313Z#000000#000#000000
modifiersName: cn=config
modifyTimestamp: 20180920082518Z
```


### 启动并设置开机启动 slapd 服务

```bash
systemctl enable slapd.service
systemctl start slapd.service
```


### 导入基本 Schema
```bash
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```

### 导入 base.ldif 文件

```bash
vim base.ldif
```

```vim
dn: dc=sitoi,dc=cn
objectClass: dcObject
objectClass: organization
dc: sitoi
o : sitoi

dn: ou=People,dc=sitoi,dc=cn
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=sitoi,dc=cn
objectClass: organizationalUnit
ou: Group
```

#### 执行导入

```bash
ldapadd -x -D cn=Manager,dc=sitoi,dc=cn -w sitoi  -f base.ldif
```


### 配置 migrationtools

```
vim /usr/share/migrationtools/migrate_common.ph
```

#### 更改以下配置
```vim
# Default DNS domain
$DEFAULT_MAIL_DOMAIN = "sitoi.cn";

# Default base
$DEFAULT_BASE = "dc=sitoi,dc=cn";
```

### 导入系统用户和组

####  利用 pl 脚本将 /etc/passwd 和 /etc/shadow 生成 LDAP 能读懂的文件格式，保存在 /tmp/ 下

```bash
/usr/share/migrationtools/migrate_base.pl > /tmp/base.ldif
/usr/share/migrationtools/migrate_passwd.pl /etc/passwd  > /tmp/passwd.ldif
/usr/share/migrationtools/migrate_group.pl /etc/group > /tmp/group.ldif
```

#### 导入 LDAP

> 需要输入管理员密码

```bash
ldapadd -x -D "cn=Manager,dc=sitoi,dc=cn" -w sitoi -f /tmp/base.ldif
ldapadd -x -D "cn=Manager,dc=sitoi,dc=cn" -w sitoi -f /tmp/group.ldif
ldapadd -x -D "cn=Manager,dc=sitoi,dc=cn" -w sitoi -f /tmp/passwd.ldif
```

### 重启服务

```bash
systemctl restart slapd
```

## Client 端

### TODO


