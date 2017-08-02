# StartSSL证书申请 #
tags: ssl
- 访问 [StartSSL网站](https://startssl.com/) （`IE`或者 `firefox`）
- 注册后倒入浏览器证书
- 登入后使用`Validations Wizard`认证域名
- 认证后使用`Certificates Wizard`生成证书

使用命令行生成`Key`和`CSR`
```Shell
root@moonright:~# openssl req -newkey rsa:2048 -keyout yourname.key -out yourname.csr
```

将`yourname.csr`中的内容拷贝到网站生成并导出`yourname.crt`。

- 使用私钥生成不需要密码的`key`

```Shell
root@moonright:~# openssl rsa -in yourname.key -out yourname.key.unsecure
```
