---
title: D1 - 实战2-Python 编码实现取网站数据并存储到数据库
share: true
description:  
category: 贵州商学院实习
daily: false
createtime: 2022-06-26 16:14
tags:
- 贵州商学院
---
## 项目内容
爬取百度热搜的数据条目，并存储到数据库
- Python
- MySQL 

## 项目目标
说明真的没必要一定要代码爬数据

https://top.baidu.com/board?tab=realtime

## 包含的知识
- Python
- BeautifulSoup
- MySQL

## 操作 步骤
- 拉取百度热搜的数据
	- urllib
	- **requests**
	- 八爪鱼
- 解析（解析需要的内容）（BeautifulSoup)
	- HTML——>DOM 文档树
	- 过滤器过滤指定的节点
	- 选择节点的内容
- 保存数据库(mysql-connector)
	- 连数据库
	- 建表
	- 构建 insert 语句
	- 执行语句
	- 关闭连接