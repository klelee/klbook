---
title: Linux开机、重启和用户登录注销
date: 2021-11-28 09:15:00
tags: 
    - Linux
categories: Linux学习笔记
---

每天总得干点啥吧！Day03

# 关机重启

- shutdown -h now			立刻进行关机（服务器一般不进行关机操作）
- shutdown -h 1                  一分钟以后关机
- shutdown -r now               现在立刻重启计算机（重启操作，推荐使用这个命令）
- halt            关机（不建议使用）
- reboot        立刻重启（可以使用，但更推荐shutdown）
- sync                 把内存数据同步到磁盘（在重启之前多执行几次）

# 用户登录和注销

## 登录

- 登录时尽量少用root账户登录
- 使用普通用户登录
- 需要使用root用户权限的时候，使用`su - root`就可以切换到root用户

## 注销

从root用户切换回普通用户使用的是`exit`或`logout`