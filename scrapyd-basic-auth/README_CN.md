[:abc: English](./README.md) | :mahjong: 简体中文 | [:back: my8100/files](https://github.com/my8100/files)

# 时隔五年，Scrapyd 终于原生支持 basic auth


## Issue in 2014
https://github.com/scrapy/scrapyd/issues/43
![issue #43](https://raw.githubusercontent.com/my8100/files/master/scrapyd-basic-auth/screenshots/issue_43.png)


## Pull request in 2019
https://github.com/scrapy/scrapyd/pull/326
![pull request #326](https://raw.githubusercontent.com/my8100/files/master/scrapyd-basic-auth/screenshots/pull_request_326.png)


## 试用
1. 安装：`pip install -U git+https://github.com/my8100/scrapyd.git@add_basic_auth`

**PR 已被合并：** `pip install -U git+https://github.com/scrapy/scrapyd.git`

2. 更新配置文件 *scrapyd.conf*，其余配置项详见[官方文档](https://scrapyd.readthedocs.io/en/latest/config.html#example-configuration-file)
```
[scrapyd]
username = yourusername
password = yourpassword
```
3. 启动：`scrapyd`
```
In [1]: import requests

In [2]: requests.get('http://127.0.0.1:6800/').status_code
Out[2]: 401

In [3]: requests.get('http://127.0.0.1:6800/', auth=('admin', 'admin')).status_code
Out[3]: 401

In [4]: requests.get('http://127.0.0.1:6800/', auth=('yourusername', 'yourpassword')).status_code
Out[4]: 200
```
4. 由于 Scrapyd 的 GitHub 最新提交已经[重构了 Jobs 页面](https://github.com/scrapy/scrapyd/pull/256)，如果正在使用 [ScrapydWeb](https://github.com/my8100/scrapydweb) 管理 Scrapyd，则需同步更新 ScrapydWeb：`pip install -U git+https://github.com/my8100/scrapydweb.git`


[返回顶部](#user-content-时隔五年scrapyd-终于原生支持-basic-auth)

[:back: my8100/files](https://github.com/my8100/files)
