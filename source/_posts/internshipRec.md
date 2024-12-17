---
title: 实习总结
excerpt: 总结一下实习中学到的技术和感想
date: 2024-12-16 11:37:00
categories:
  - 技术
tags:
  - 编程经验
banner_img: /img/post/fulilian.png
---

## 微服务架构

### 什么是微服务

微服务架构是将一个较大的应用系统拆分成多个较小的服务，每个服务负责一个特定的功能，服务之间通过 HTTP 或 RPC 进行通信。每个服务都可以独立部署、扩展和维护，从而提高了系统的可扩展性、可维护性和可测试性。

比如将一个电商网站拆分成商品服务、订单服务、用户服务、支付服务、物流服务等多个服务，这样子每个服务可以单独开发，通过 HTTP 或 RPC 进行通信，独立部署，互不影响。这样一旦某个服务宕机也不会影响整体服务，而且也可以快速重启。

类比一下，微服务架构就像积木玩具，可以随意组合、新增、删除，还可以很方便地复制已有的部分。


## 后端技术选型

### Go vs Java vs C++

在学校做数据库写C++，美团实习的时候做后端写Java，现在实习做后端写Go

C++ 性能比较好，程序员可以自己进行内存管理，但开发成本比较高，而且多线程的管理和通信比较麻烦。

Java JVM管理内存，省去很多麻烦，但性能不如 C++。有许多功能强大的库，不过架构过于庞大？

Go 支持指针，也有内存管理，实际开发下来，有点函数式编程的感觉，每个包只需要管理自己导入的库，而且kafka、redis等中间件的使用也比较简单

### hertz

Hertz[həːts] 是一个 Golang 微服务 HTTP 框架，在设计之初参考了其他开源框架 fasthttp、gin、echo 的优势， 并结合字节跳动内部的需求，使其具有高易用性、高性能、高扩展性等特点，目前在字节跳动内部已广泛使用。 如今越来越多的微服务选择使用 Golang，如果对微服务性能有要求，又希望框架能够充分满足内部的可定制化需求，Hertz 会是一个不错的选择。
[hertz文档](https://cloudwego.cn/zh/docs/hertz/overview/)

### mysql vs PostgreSql

这块我不太懂，不过MySql的应用更广，所以还是MySql
[mysql和postgresql的对比](https://blog.csdn.net/weixin_43298211/article/details/139929527)

## 架构设计思考

状态判断在工作流的task内还是外
外 这样的话task只需要完成自己的逻辑，不需要关心状态，状态由工作流来维护
内 这样的话task需要维护自己的状态，工作流只需要调用task的接口，不需要关心状态

machinery工作流无法判断状态再进入任务，所以还是在内，每个task判断自己的状态，再决定是否执行

## 多语言、多分辨率的存储

当业务需要出海或者多分辨率的时候，需要存储多语言、多分辨率的文件，比如视频、图片、音频等

### 使用主从表

```sql
CREATE TABLE resources (
    id BIGINT AUTO_INCREMENT PRIMARY KEY COMMENT '资源ID',
    resource_type VARCHAR(64) NOT NULL COMMENT '资源类型，例如：视频、图片等',
    file_name VARCHAR(255) NOT NULL COMMENT '资源文件名称',
    description TEXT COMMENT '资源描述',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间'
) COMMENT = '资源的基本信息，例如文件名、资源类型等';

CREATE TABLE resource_translations (
    id BIGINT AUTO_INCREMENT PRIMARY KEY COMMENT 'ID',
    resource_id BIGINT NOT NULL COMMENT '资源ID (外键，关联到 resources 表)',
    language_code VARCHAR(10) NOT NULL COMMENT '语言代码，例如 en, zh, fr 等',
    title VARCHAR(255) COMMENT '资源标题的翻译',
    description TEXT COMMENT '资源描述的翻译',
    FOREIGN KEY (resource_id) REFERENCES resources(id) ON DELETE CASCADE
) COMMENT = '存储资源的不同语言版本，使用语言代码区分';

CREATE TABLE resource_resolutions (
    id BIGINT AUTO_INCREMENT PRIMARY KEY COMMENT '分辨率ID',
    resource_id BIGINT NOT NULL COMMENT '资源ID (外键，关联到 resources 表)',
    resolution VARCHAR(20) NOT NULL COMMENT '分辨率，例如 720p, 1080p, 4k 等',
    file_url VARCHAR(512) NOT NULL COMMENT '资源文件的URL地址或路径',
    file_size BIGINT COMMENT '文件大小（字节）',
    FOREIGN KEY (resource_id) REFERENCES resources(id) ON DELETE CASCADE
) COMMENT = '存储资源的不同分辨率版本';

```

## PR的合并方式

merge
这种情况下，pr包含的commit_ids（源分支的所有提交记录）会同步到中央仓库目标分支里面，合并之后目标分支上还会产生一个merge commit id。这个merge commit id就是将pr包含的commit_id（源分支的所有提交记录）会同步到中央仓库目标分支这个操作的提交。

squash and merge（常用）
这种情况下，pr包含的commit_ids（源分支的所有提交记录）不同步中央仓库目标分支里面，这种方式，当前分支（源分支）的多个提交会被压缩，在目标分支上形成一个提交(merge commit id)，这个merge commit id就代表压缩源分支多个提交这个操作。

2.3 rebase and merge
这种情况下，pr包含的commit_ids（源分支的所有提交记录）通过变基，在目标分支上形成多个新的commit id逐个应用到目标分支上。这些新的提交会按照原始提交的顺序添加到目标分支上，形成一个线性的提交历史。因此变基（rebase）操作不会产生合并提交（merge commit id）。

## 目录用日期的md5加密

将用户上传的文件放在用md5加密的日期目录里面，既做了数据防护，也方便了数据管理

>8c3dfe799ad1d5b312e6a636fef16f2c/video.mp4


