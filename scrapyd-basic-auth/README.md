:abc: English | [:mahjong: 简体中文](./README_CN.md) | [:back: my8100/files](https://github.com/my8100/files)

# Native support for basic auth finally comes to Scrapyd after a 5-year wait


## Issue in 2014
https://github.com/scrapy/scrapyd/issues/43
![issue #43](https://raw.githubusercontent.com/my8100/files/master/scrapyd-basic-auth/screenshots/issue_43.png)


## Pull request in 2019
https://github.com/scrapy/scrapyd/pull/326
![pull request #326](https://raw.githubusercontent.com/my8100/files/master/scrapyd-basic-auth/screenshots/pull_request_326.png)


## Try it out
1. Install via `pip install -U git+https://github.com/my8100/scrapyd.git@add_basic_auth`

**PR merged:** `pip install -U git+https://github.com/scrapy/scrapyd.git`

2. Update the *scrapyd.conf* file, check out the  [official docs](https://scrapyd.readthedocs.io/en/latest/config.html#example-configuration-file) for more info
```
[scrapyd]
username = yourusername
password = yourpassword
```
3. Run command `scrapyd`
```
In [1]: import requests

In [2]: requests.get('http://127.0.0.1:6800/').status_code
Out[2]: 401

In [3]: requests.get('http://127.0.0.1:6800/', auth=('admin', 'admin')).status_code
Out[3]: 401

In [4]: requests.get('http://127.0.0.1:6800/', auth=('yourusername', 'yourpassword')).status_code
Out[4]: 200
```
4. Note that you have to update [ScrapydWeb](https://github.com/my8100/scrapydweb) as the Jobs page of Scrapyd was refactored in [PR #256](https://github.com/scrapy/scrapyd/pull/256): `pip install -U git+https://github.com/my8100/scrapydweb.git`


[Jump to top](#user-content-native-support-for-basic-auth-finally-comes-to-scrapyd-after-a-5-year-wait)

[:back: my8100/files](https://github.com/my8100/files)
