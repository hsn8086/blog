---
title: 强大的包管理-Poetry
date: 2024-9-5 00:00:00
tags:
  - 编程
  - python
  - 工具
  - 介绍
reprintPolicy: cc_by_nc_nd
---

# 简介
> Poetry是一个用于管理python包的命令行工具.
# 有何优势?
pip可以安装来自pypi的包,而poetry不止于安装pypi的包,他还可以自动的从源码安装,从远程目标安装,甚至是从远程git仓库安装.
而包管理只是poetry的一部分,同样重要的是poetry的打包发布和环境管理的功能.自动化的打包发布,极大节省了开发者的时间.强大的坏境管理,使其拥有纯虚拟环境无法比拟的优势.
# 如何安装?
poetry有多种安装方式,但其中使用pip是不推荐的,使用pip安装容易污染环境.
注: poetry需要python3.8及以上版本.
## 官方脚本
### Windows (PowerShell)
``` powershell
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | py -
```
### Linux, macOS, Windows (WSL)
``` bash
curl -sSL https://install.python-poetry.org | python3 -
```
## pipx
``` bash
pipx install poetry
```
# 基本用法
## 初始化
- 新项目
``` bash
poetry new your_project_name
```
- 现有非poetry项目
``` bash
poetry init
```
- 现有poetry项目
``` bash
poetry install
```

### poetry new
poetry new将会在当前工作目录下创建一个项目文件夹以及项目脚手架.
### poetry init
poetry init将会以工作目录为项目文件夹创建poetry所需的配置文件.
### poetry install
poetry install会根据配置文件安装项目所需依赖.
## 激活虚拟环境
### 直接运行命令行
``` bash
poetry run your command
```
例如运行start.py:
``` bash
poetry run python start.py
```
### 启用虚拟环境shell
``` bash
poetry shell
```
即可进入虚拟环境.
## 安装软件包
poetry可以从各种途径安装软件包,除了默认的pypi,还可以从网络甚至是git安装.
``` bash
poetry add package_name # 从pypi添加包
poetry add https://github.com/Suto-Commune/anyquote/archive/refs/tags/1.0.0.tar.gz # 从源码添加
poetry add https://github.com/Suto-Commune/sutowebdav.git # 从git安装
poetry add pytest --dev # 添加包至dev组
```
### 换源
在pyproject.toml中加入以下段落即可:
``` toml
[plugins.pypi_mirror]
url = "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/"
priority = "primary"
```
或是运行命令行:
``` bash
poetry source add --priority=primary mirrors https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/
```
## 卸载软件包
``` bash
poetry remove package_name
```
## 更新软件包
```bash
poetry update # 更新所有软件包
poetry update package_name # 更新指定包
```
# 打包上传
## 打包
``` bash
poetry build
```
运行此命令后poetry会自动将项目打包并在dist目录下输出源码压缩文件和wheel.
## 上传
运行命令后输入token即可上传.
```
poetry publish
```
\* 请在打包后上传.
# 导出为Requirements.txt
第一次使用poetry时疑惑过,poetry会不会提高安装门槛,后来看来是没必要担心的,poetry支持导出项目依赖:
``` bash
poetry export --format requirements.txt --output requirements.txt --without-hashes
```
