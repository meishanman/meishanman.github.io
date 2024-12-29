# 获取Let's Encrypt证书

下载官方工具：
``` bash
$ git clone https://github.com/letsencrypt/letsencrypt
```
获取证书
``` bash
$ cd letsencrypt
$ ./certbot-auto certonly  -d *.chenjunjun.com -d chenjunjun.com --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory
```

这里为两组域名添加了证书，所以验证域名时需要验证两次。生成的证书路径以及文件：
``` bash
$ sudo ls /etc/letsencrypt/live/chenjunjun.com/
cert.pem  chain.pem  fullchain.pem  privkey.pem  README
```

参考：
- [Let's Encrypt 终于支持通配符证书了](https://www.jianshu.com/p/c5c9d071e395)

