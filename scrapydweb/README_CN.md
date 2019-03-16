[:abc: English](./README.md) | :mahjong: 简体中文 | [:back: ScrapydWeb](https://github.com/my8100/scrapydweb)

# 如何简单高效地部署和监控分布式爬虫项目

![twitter](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/twitter.png)


## 安装和配置
1. 请先确保所有主机都已经安装和启动 [Scrapyd](https://github.com/scrapy/scrapyd)，如果需要远程访问 Scrapyd，则需将 Scrapyd 配置文件中的 [bind_address](https://scrapyd.readthedocs.io/en/latest/config.html#bind-address) 修改为 `bind_address = 0.0.0.0`，然后重启 Scrapyd service。
2. 开发主机或任一台主机安装 [*ScrapydWeb*](https://github.com/my8100/scrapydweb)：`pip install scrapydweb`
3. 通过运行命令 `scrapydweb` 启动 *ScrapydWeb*（首次启动将自动在当前工作目录生成配置文件）。
4. 启用 HTTP 基本认证（可选）：
```python
ENABLE_AUTH = True
USERNAME = 'username'
PASSWORD = 'password'
```
5. 添加 Scrapyd server，支持字符串和元组两种配置格式，支持添加认证信息和分组/标签：
```python
SCRAPYD_SERVERS = [
    '127.0.0.1',
    # 'username:password@localhost:6801#group',
    ('username', 'password', 'localhost', '6801', 'group'),
]
```
6. 运行命令 `scrapydweb` 重启 *ScrapydWeb*。


## 访问 web UI
通过浏览器访问并登录 http://127.0.0.1:5000。
* Servers 页面自动输出所有 Scrapyd server 的运行状态。
* 通过分组和过滤可以自由选择若干台 Scrapyd server，然后在上方 Tabs 标签页中选择 Scrapyd 提供的任一 [HTTP JSON API](https://scrapyd.readthedocs.io/en/latest/api.html)，实现**一次操作，批量执行**。

![servers](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/servers.png)

* 通过集成 [*LogParser*](https://github.com/my8100/logparser)，Jobs 页面自动输出爬虫任务的 pages 和 items 数据。
* *ScrapydWeb* 默认通过定时创建快照将爬虫任务列表信息保存到数据库，即使重启 Scrapyd server 也不会丢失任务信息。([issue 12](https://github.com/scrapy/scrapyd/issues/12))

![jobs](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/jobs.png)


## 部署项目
* 通过配置 `SCRAPY_PROJECTS_DIR` 指定 Scrapy 项目开发目录，*ScrapydWeb* 将自动列出该路径下的所有项目，默认选定最新编辑的项目，选择项目后即可**自动打包**和部署指定项目。
* 如果 *ScrapydWeb* 运行在远程服务器上，除了通过当前开发主机上传常规的 egg 文件，也可以将整个项目文件夹添加到 zip/tar/tar.gz 压缩文件后直接上传即可，无需手动打包为 egg 文件。
* **支持一键部署项目到 Scrapyd server 集群。**

![deploy](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/deploy.gif)


## 运行爬虫
* 通过下拉框依次选择 project，version 和 spider。
* 支持传入 Scrapy settings 和 spider arguments。
* 支持创建基于 [APScheduler](https://github.com/agronholm/apscheduler) 的定时爬虫任务。(如需同时启动大量爬虫任务，则需调整 Scrapyd 配置文件的 [max-proc](https://scrapyd.readthedocs.io/en/stable/config.html#max-proc) 参数)
* **支持在 Scrapyd server 集群上一键启动分布式爬虫。**

![run](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/run.gif)


## 日志分析和可视化
* **如果在同一台主机运行 Scrapyd 和 *ScrapydWeb*，建议设置 `SCRAPYD_LOGS_DIR` 和 `ENABLE_LOGPARSER`**，则启动 *ScrapydWeb* 时将自动运行 [*LogParser*](https://github.com/my8100/logparser)，该子进程通过定时增量式解析指定目录下的 Scrapy 日志文件以加快 Stats 页面的生成，避免因请求原始日志文件而占用大量内存和网络资源。
* **同理，如果需要管理 Scrapyd server 集群，建议在其余主机单独安装和启动 *LogParser***。
* 如果安装的 Scrapy 版本不大于 1.5.1，*LogParser* 将能够自动通过 Scrapy 内建的 [Telnet Console](https://scrapy.readthedocs.io/en/latest/topics/telnetconsole.html) 读取 Crawler.stats 和 Crawler.engine 数据，以便掌握 Scrapy 内部运行状态。

![stats](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/stats.gif)


## 定时爬虫任务
* 支持查看爬虫任务的参数信息，追溯历史记录
* 支持**暂停，恢复，触发，停止，编辑和删除**任务等操作

![tasks](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/tasks.gif)


## 邮件通知
通过轮询子进程在后台定时模拟访问 Stats 页面，*ScrapydWeb* **将在满足特定触发器时根据设定自动停止爬虫任务并发送通知邮件**，邮件正文包含当前爬虫任务的统计信息。

1. 添加邮箱帐号：
```python
SMTP_SERVER = 'smtp.qq.com'
SMTP_PORT = 465
SMTP_OVER_SSL = True
SMTP_CONNECTION_TIMEOUT = 10

EMAIL_USERNAME = ''  # defaults to FROM_ADDR
EMAIL_PASSWORD = 'password'
FROM_ADDR = 'username@qq.com'
TO_ADDRS = [FROM_ADDR]
```

2. 设置邮件工作时间和基本触发器，以下示例代表：每隔1小时或当某一任务完成时，并且当前时间是工作日的9点，12点和17点，*ScrapydWeb* 将会发送通知邮件。
```python
EMAIL_WORKING_DAYS = [1, 2, 3, 4, 5]
EMAIL_WORKING_HOURS = [9, 12, 17]
ON_JOB_RUNNING_INTERVAL = 3600
ON_JOB_FINISHED = True
```

3. 除了基本触发器，*ScrapydWeb* **还提供了多种触发器用于处理不同类型的 log**，包括 'CRITICAL', 'ERROR', 'WARNING', 'REDIRECT', 'RETRY' 和 'IGNORE'等。
```python
LOG_CRITICAL_THRESHOLD = 3
LOG_CRITICAL_TRIGGER_STOP = True
LOG_CRITICAL_TRIGGER_FORCESTOP = False
# ...
LOG_IGNORE_TRIGGER_FORCESTOP = False
```
以上示例代表：当日志中出现3条或以上的 critical 级别的 log 时，*ScrapydWeb* **将自动停止当前任务**，如果当前时间在邮件工作时间内，则同时发送通知邮件。


## 移动端 UI

![mobileui](https://raw.githubusercontent.com/my8100/files/master/scrapydweb/screenshots/mobileui.png)


[返回顶部](#user-content-如何简单高效地部署和监控分布式爬虫项目)

[:back: ScrapydWeb](https://github.com/my8100/scrapydweb)
