# Ghostscript 项目模板

该项目模板是一个基于 GhostScript 的 PDF 转 JPG 工具，该项目是一个 funcraft 工程，借助 funcraft 工具进行依赖安装，并部署代码到阿里云的函数计算平台，作为一个 serverless 的 PDF 转换服务。

## 依赖工具

本项目是在 MacOS 下开发的，涉及到的工具是平台无关的，对于 Linux 和 Windows 桌面系统应该也同样适用。在开始本例之前请确保如下工具已经正确的安装，更新到最新版本，并进行正确的配置。

* [Docker](https://www.docker.com/)
* [Funcraft](https://github.com/alibaba/funcraft)

对于 MacOS 用户可以使用 [homebrew](https://brew.sh/) 进行安装：

```bash
brew cask install docker
brew tap vangie/formula
brew install fun
```

Windows 和 Linux 用户安装请参考：

1. https://github.com/aliyun/fun/blob/master/docs/usage/installation.md
2. https://github.com/aliyun/fcli/releases

安装好后，记得先执行 `fun config` 初始化一下配置。

## 初始化

使用 fun init 命令可以快捷地将本模板项目初始化到本地。

```bash
fun init vangie/ghostscript-example
```

## 安装依赖

```bash
$ fun install

Installing recursively on fun.yml

skip pulling image aliyunfc/runtime-python3.6:build-1.6.1...
Task => workaround for update-gsfontmap
     => bash -c  'mkdir -p /code/.fun/root/etc/ghostscript/cidfmap.d/ && mkdir -p /code/.fun/root/etc/ghostscript/fontmap.d/ && mkdir -p /etc/ghostscript/ && mkdir -p /var/lib/ghostscript/ && mkdir -p /code/.fun/root/var/lib/ghostscript/fonts && ln -s /code/.fun/root/etc/ghostscript/cidfmap.d /etc/ghostscript/ && ln -s /code/.fun/root/etc/ghostscript/fontmap.d /etc/ghostscript/ && ln -s /code/.fun/root/var/lib/ghostscript/fonts /var/lib/ghostscript/'
Task => [UNNAMED]
     => apt-get update (if need)
     => apt-get install -y -d -o=dir::cache=/code/.fun/tmp/install ghostscript --reinstall
     => bash -c 
        for f in $(ls /code/.fun/tmp/install/archives/*.deb); do
          dpkg -x $f /code/.fun/root; 
          mkdir -p /code/.fun/tmp/install/deb-control/${f%.*}; 
          dpkg -e $f /code/.fun/tmp/install/deb-control/${f%.*}; 

          if [ -f "/code/.fun/tmp/install/deb-control/${f%.*}/postinst" ]; then 
            FUN_INSTALL_LOCAL=true /code/.fun/tmp/install/deb-control/${f%.*}/postinst configure;
          fi; 
        done;

Creating config file /etc/papersize with new version
     => bash -c 'rm -rf /code/.fun/tmp/install/archives'
```

## 本地调用

```bash
$ fun local invoke pdf2jpg
using template: template.yml
skip pulling image aliyunfc/runtime-nodejs10:1.6.1...
FC Invoke Start RequestId: 21d9c646-1db4-403c-b018-cd4246e193d3
load code for handler:index.handler
2019-09-18T09:45:38.400Z 21d9c646-1db4-403c-b018-cd4246e193d3 [verbose] stdout =================== START
2019-09-18T09:45:38.400Z 21d9c646-1db4-403c-b018-cd4246e193d3 [verbose] GPL Ghostscript 9.26 (2018-11-20)
Copyright (C) 2018 Artifex Software, Inc.  All rights reserved.
This software comes with NO WARRANTY: see the file PUBLIC for details.
Processing pages 1 through 1.
Page 1

2019-09-18T09:45:38.401Z 21d9c646-1db4-403c-b018-cd4246e193d3 [verbose] stdout =================== END
FC Invoke End RequestId: 21d9c646-1db4-403c-b018-cd4246e193d3
convert success.
JPG file save to /tmp/test.jpg
2019-09-18T09:45:38.416Z 21d9c646-1db4-403c-b018-cd4246e193d3 [error] (node:21) [DEP0005] DeprecationWarning: Buffer() is deprecated due to security and usability issues. Please use the Buffer.alloc(), Buffer.allocUnsafe(), or Buffer.from() methods instead.


RequestId: 21d9c646-1db4-403c-b018-cd4246e193d3          Billed Duration: 2132 ms        Memory Size: 1998 MB    Max Memory Used: 78 MB
```

可以查看文件 .fun/tmp/invoke/ghostscript/pdf2jpg/test.jpg ，预留转换后的效果。

## 部署

```bash
$ fun deploy
using template: template.yml
using region: cn-shanghai
using accountId: ***********4733
using accessKeyId: ***********EUz3
using timeout: 60

Waiting for service ghostscript to be deployed...
        Waiting for function pdf2jpg to be deployed...
                Waiting for packaging function pdf2jpg code...
                The function pdf2jpg has been packaged. A total of 1054 files files were compressed and the final size was 23.44 MB
        function pdf2jpg deploy success
service ghostscript deploy success
```

## 执行

```bash
$ fun invoke pdf2jpg
fun invoke pdf2jpg
using template: template.yml
========= FC invoke Logs begin =========
FC Invoke Start RequestId: 1411066b-1ad0-4750-922d-2350652ca5a6
load code for handler:index.handler
2019-09-18T09:52:39.802Z 1411066b-1ad0-4750-922d-2350652ca5a6 [verbose] stdout =================== START
2019-09-18T09:52:39.802Z 1411066b-1ad0-4750-922d-2350652ca5a6 [verbose] GPL Ghostscript 9.26 (2018-11-20)
Copyright (C) 2018 Artifex Software, Inc.  All rights reserved.
This software comes with NO WARRANTY: see the file PUBLIC for details.
Processing pages 1 through 1.
Page 1

2019-09-18T09:52:39.802Z 1411066b-1ad0-4750-922d-2350652ca5a6 [verbose] stdout =================== END
FC Invoke End RequestId: 1411066b-1ad0-4750-922d-2350652ca5a6

Duration: 513.34 ms, Billed Duration: 600 ms, Memory Size: 128 MB, Max Memory Used: 56.49 MB
========= FC invoke Logs end =========

FC Invoke Result:
convert success.
JPG file save to /tmp/test.jpg
```

## 参考阅读

1. https://yq.aliyun.com/articles/717083
