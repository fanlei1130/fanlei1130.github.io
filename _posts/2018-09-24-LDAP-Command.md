---
layout:     post
title:      "OpenLDAP Command"
subtitle:   ""
date:       2018-09-24 12:00:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - OpenLDAP
    - 权限验证
    - 命令
---


# LDAP Command

### ldapsearch

> ldapsearch - ldap 搜索工具

ldapsearch 实用程序可打开与 LDAP 服务器的连接，使用过滤器 filter 绑定并执行搜索。

如果 ldapsearch 找到一个或多个条目，则会检索由 attrs 指定的属性并且会将条目和值输出到标准输出。如果没有列出 attrs，则会返回所有属性。

|选项|描述|
|:---:|:---:|
| `-d` | debuglevel 设置 LDAP 调试级别。适用于 ldapdelete 的有用调试级别包括：1:跟踪 2:包 4:选项 32:过滤器 128:访问控制 要请求多个类别的调试信息，请将掩码相加。例如，要请求跟踪和过滤器信息，请将 debuglevel 指定为 33。
| `-x` | 进行简单认证 |
| `-D` | 用来绑定服务器的DN |
| `-w` | 绑定DN的密码 |
| `-b` | 指定要查询的根节点 |
| `-H` | 制定要查询的服务器 |

**例子**

###### 查询所有用户

```bash
ldapsearch -x -b "dc=sitoi,dc=cn" -H ldap://192.168.1.143
```

###### 指定条件的查询

```bash
ldapsearch -x -b "dc=sitoi,dc=cn" "uid=demo" -H ldap://192.168.1.143
```

###### 或条件查询配合正则匹配

```bash
ldapsearch -x -b "dc=sitoi,dc=cn" "(|(uid=*de*)(cn=*Ada Cather*))" -H ldap://192.168.1.143
```

###### 与条件查询配合正则匹配

```bash
ldapsearch -x -b "dc=sitoi,dc=cn" "(&(uid=*de*)(cn=*Ada Cather*))" -H ldap://192.168.1.143
```


### ldapadd

> ldapadd - ldap 条目添加工具

ldapadd 实用程序是作为到 ldapmodify 工具的硬链接实现的。当作为 ldapadd 调用时，会自动打开 –a（添加新条目）选项。

|选项|描述|
|:---:|:---:|
| `-x` | 进行简单认证 |
| `-D` | 用来绑定服务器的DN |
| `-h` | 目录服务的地址 |
| `-w` | 绑定DN的密码 |
| `-f` | 使用ldif文件进行条目添加的文件 |

**例子**

```bash
ldapadd -x -D "cn=root,dc=sitoi,dc=cn" -w sitoi -f demo.ldif
ldapadd -x -D "cn=root,dc=sitoi,dc=cn" -w sitoi #(这样写就是在命令行添加条目)
```



### ldappasswd

> ldapmodify - ldap 密码修改工具

ldapmodify 实用程序可打开与 LDAP 服务器的连接，修改条目密码。


|选项|描述|
|:---:|:---:|
| `-x` | 进行简单认证 |
| `-D` | 用来绑定服务器的DN |
| `-w` | 绑定DN的密码 |
| `-S` | 提示的输入密码 |
| `-s` | pass 把密码设置为pass |
| `-a` | pass 设置old passwd为pass |
| `-A` | 提示的设置old passwd |
| `-H` | 是指要绑定的服务器 |
| `-I` | 使用sasl会话方式 |

**例子**

```bash
ldappasswd -x -D 'cm=root,dc=sitoi,dc=cn' -w sitoi 'uid=Sitoi,dc=sitoi,dc=cn' -S
```

```text
New password:
Re-enter new password:
```

就可以更改密码了，如果原来记录中没有密码，将会自动生成一个userPassword。

### ldapmodify

> ldapmodify - ldap 条目修改工具

ldapmodify 实用程序可打开与 LDAP 服务器的连接，绑定并修改或添加条目。条目信息是从标准输入或者从使用 –f 选项指定的 file 中读取的。ldapadd 实用程序是作为到 ldapmodify 工具的硬链接实现的。当作为 ldapadd 调用时，会自动打开 –a（添加新条目）选项。

ldapadd 和 ldapmodify 都拒绝同一条目的重复属性名/值对。

|选项|描述|
|:---:|:---:|
| `-a` | 添加新的条目.缺省的是修改存在的条目. |
| `-C` | 自动追踪引用. |
| `-c` | 出错后继续执行程序并不中止.缺省情况下出错的立即停止.比如如果你的ldif 文件内的某个条目在数据库内并不存在,缺省情况下程序立即退出,但如果使用了该选项,程序忽略该错误继续执行. |
| `-n` | 用于调试到服务器的通讯.但并不实际执行搜索.服务器关闭时,返回错误；服务器打开时,常和-v 选项一起测试到服务器是否是一条通路. |
| `-v` | 运行在详细模块.在标准输出中打出一些比较详细的信息.比如:连接到服务器的ip地址和端口号等. |
| `-M[M]` | 打开manage DSA IT 控制. -MM 把该控制设置为重要的. |
| `-f` | file 从文件内读取条目的修改信息而不是从标准输入读取. |
| `-x` | 使用简单认证. |
| `-D` | binddn 指定搜索的用户名(一般为一dn 值). |
| `-W` | 指定了该选项,系统将弹出一提示入用户的密码.它和-w 选项相对使用. |
| `-w` | bindpasswd 直接指定用户的密码. 它和-W 选项相对使用. |
| `-H` | ldapuri 指定连接到服务器uri(ip 地址和端口号,常见格式为 ldap://hostname:port).如果使用了-H 就不能使用-h 和-p 选项. |
| `-h` | ldaphost 指定要连接的主机的名称/ip 地址.它和-p 一起使用. |
| `-p` | ldapport 指定要连接目录服务器的端口号.它和-h 一起使用.如果使用了-h 和-p 选项就不能使用-H 选项. |
| `-Z[Z]` | 使用StartTLS 扩展操作.如果使用-ZZ,命令强制使用StartTLS 握手成功. |
| `-V` | 启用证书认证功能,目录服务器使用客户端证书进行身份验证,必须与-ZZ 强制启用TLS 方式配合使用,并且匿名绑定到目录服务器. |
| `-e` | 设置客户端证书文件,例: -e cert/client.crt |
| `-E` | 设置客户端证书私钥文件,例: -E cert/client.key |

**例子**

```bash
ldapmodify -x -D "cn=root,dc=sitoi,dc=cn" -W -f modify.ldif
```
将 `modify.ldif` 中的记录 更新 原有的记录。

### ldapdelete

> ldapdelete - ldap 删除条目工具

ldapmodify 实用程序可打开与 LDAP 服务器的连接，绑定并修改或添加条目。条目信息是从标准输入或者从使用 –f 选项指定的 file 中读取的。ldapadd 实用程序是作为到 ldapmodify 工具的硬链接实现的。当作为 ldapadd 调用时，会自动打开 –a（添加新条目）选项。

ldapadd 和 ldapmodify 都拒绝同一条目的重复属性名/值对。

|选项|描述|
|:---:|:---:|
| -d debuglevel | 设置 LDAP 调试级别。适用于 ldapdelete 的有用调试级别包括：1:跟踪 2:包 4:选项 32:过滤器 128:访问控制 要请求多个类别的调试信息，请将掩码相加。例如，要请求跟踪和过滤器信息，请将 debuglevel 指定为 33。
| -D bindDN | 使用标识名 bindDN 绑定到目录。
| -f file | 从 file 而不是从标准输入读取条目删除信息。
| -h ldaphost | 指定运行 LDAP 服务器的备用主机。
| -p ldapport | 指定 LDAP 服务器侦听的备用 TCP 端口。
| -W password | 指定在 –P 选项中给出的客户端密钥数据库的口令。对于基于证书的客户端验证，此选项是必需的。在命令行上指定 password 会有安全问题，因为系统上的其他人可以通过 ps 命令看到口令。请改用 –j 从文件中指定口令。此选项与 –j 互斥。
| -w passwd | 使用 passwd 作为用于对目录进行验证的口令。当使用 –w passwd 指定用于验证的口令时，系统的其他用户可以通过 ps 命令在脚本文件中或者在 shell 历史记录中看到口令。如果在不使用此选项的情况下使用 ldapdelete 命令，则该命令将提示输入口令并从标准输入中读取口令。不与 –w 选项一起使用时，其他用户将看不到口令。

**例子**

```bash
ldapdelete -x -D "cn=Manager,dc=sitoi,dc=cn" -w sitoi "uid=Sitoi,ou=People,dc=sitoi,dc=cn"
```

> Tips:
>> 如果o或ou中有成员是不能删除的,那么o或ou不能删除。
