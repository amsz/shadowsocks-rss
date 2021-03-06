# 服务端常见问题排查

如果遇到问题，可按以下次序排查：

1. 检查服务端与客户端配置是否一致
2. 防火墙端口有没有打开
3. 服务端对应端口有没有打开（查log）
4. 服务器的UTC时间是否和客户端误差超过一天
5. 查看服务端log，检测对应端口开启的配置是否与预期相同
6. 检查服务端配置，protocol及obfs两字段绝对不可以留空（即设置为""）
7. 服务端是否开启了两个导致占用相同端口（通过 `ps -ef|grep python` 查找python进程）
8. 检查是否有其它进程占用相同端口，如nginx/apache占了80/443，同时你开了这两端口
9. 如果A客户端连得上，B客户端连不上，至少证明服务端正常，要确认服务端此端口允许的客户端数目不小于3个，然后确保是否在同一网络环境及同一配置，如果是，可能是客户端的问题，否则可能是网络问题，需要更多信息分析
10. 如果服务端某端口允许的客户端数目只有一个，那么你客户端重新启动时，需要等待3分钟再连接此端口，否则会认为超出客户端最大数量
11. `create encryptor fail at port xxx`此log表示你使用了一个不支持的加密，例如你没有安装libsodium然后使用chacha20
12. `obfs plugin xxxx not supported`此log表示你使用了一个不支持的协议或混淆，例如`auth_aes128_md5_compatible`，此协议不支持兼容
13. `found an error in config.json: xxx`此log表示你的配置文件格式错误，请检查此log所写的行和列位置上的内容
14. `[Errno 98] Address already in use`此log表示你端口被占用，请看第7条和第8条
15. `wrong timestamp, time_dif xxx`或`tls_auth wrong time`表示UTC时间误差过大，参见第4条
16. `unsupported addrtype 69, maybe wrong password or encryption method`SSR特有的错误信息（addrtype 69），说明使用的是SSR配置，但客户端配置不正确，或不是SSR客户端的连接请求，均会报这个错误，更多信息需要参考此错误的上下文，如果连接IP不是你的，那么可无视
17. `tls_auth wrong sessionid_len`这个错误和你无关，通常是普通浏览器或扫描器或主动探测器等发出的非SSR错误请求
18. `tls_auth wrong sha1`很可能就是密码错误
19. `replay attack detect`或`deprecated id, someone replay attack`表示你中奖了，有人在尝试重放攻击
20. 如果服务器运行一段时间后最后输出结果是killed（screen方式运行可以看到），那么检查swap是不是为0，建议设置swap为1024M以上
21. 如果服务器运行一段时间后不稳定，有时能连接上有时不能，那么检查log里，服务端刚运行时的输出，应该会包含一行类似`current process RLIMIT_NOFILE resource: soft 1024 hard 4096`，如果你看到这个soft和hard的数值不超过10000，那么你应该修改此值，[参见这里](https://github.com/breakwa11/shadowsocks-rss/wiki/ulimit)