---
id: server-guides-cdn
title: CDN
---

## 文件系统

### 本地

文件系统 - 本地 的存在为有条件的用户提供了独立部署的支持，其中文件存放在 `storage/app` 目录中。用户可以使用独立的系统对文件进行发布，只需要将公开地址填写进去即可。

### 本地公开

「本地公开」是在 mts 中默认的一种方式，主要是便于没有条件的用户简单的拥有储存功能。

#### 设置

进入「CDN 管理」，CDN 项选择「文件系统」，然后磁盘选择「本地公开」

#### 必要命令

默认情况下，本地公开磁盘是放在 `/storage/app/public` 目录下的，这个目录并非公开目录，你需要实现：

```shell
php artisan storage:link
```

执行完成后会创建一个软链到 `public/storage`。此时就可以通过你的域名访问文件了。

### AWS S3

亚马逊 S3 磁盘，是使用你的服务器作为中转站，最重将资源存储在 S3 服务器上的一种磁盘服务。

起到节省磁盘空间的目的。

#### 安装

在使用 S3 磁盘之前，你需要执行：

```shell
composer require league/flysystem-aws-s3-v3 ~1.0
```

来安装 S3 的 磁盘兼容软件。

## 七牛

七牛的驱动支持七牛的两项服务，你可以选择其中一项，也可以选择同时使用。

### 配置说明

使用七牛必须将后台表单配置完成，否则服务将会出现错误。

这里推荐将签字设置为「私有」以达到保护资源的目的。

你需要在七牛的「回源设置」中填写「app_url/storage/」作为回源地址，这里暂时的，你也可以点击「[CDN 部署方案](/plus-docs/core/1.0/cdn/programme)」来使用独立系统配置。

### 对象存储

使用对象存储支持 cdn 同时使用，这是七牛自带的功能，不需要在 mts 后台设置。

### 融合 CDN

使用融合 CDN 唯一需要注意的是，你需要联系七牛客服开通「目录刷新」功能，因为例如 头像 在内的资源，无法准确的获取需要刷新的资源。

## 部署推荐方案

这里的 CDN 部署方案我们以「七牛」为例，其他也可以使用。

### 说明

因为使用「本地公开」磁盘作为存储，但是在方案中，你不需要执行 `storage:link` 目录将其公开。

### 文件系统

这里，你可以采用一套独立的文件系统开发布你的资源，资源存储在 `storage/app/public` 目录中，如果你使用云服务器，建议使用远程云储存对该目录进行挂载。

部署完成后，你最好开启「回源验证」对你资源服务器的每一个资源获取请求进行验证，以避免一些用户了解 mts 规则进行免费获取收费内容。

### 方案2

我们并不推荐有条件的大型用户直接使用 mts 的文件系统和 CDN 系统，因为这是针对 90% 用户需求做的通用解决方案。

所以，如果你是那 10% 我们建议你针对 mts 的上传，获取，生成 CDN 进行针对某一个云存储或者私有存储进行针对性的开发，其中您至少做到：

- 文件直传
- 来源校验
- 储存任务完成后的数据表记录创建
- public 地址生成
- 付费资源的源头拦截验证