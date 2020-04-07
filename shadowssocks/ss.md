# ss 安装

```shell
# 下载 anaconda
wget https://repo.anaconda.com/archive/Anaconda3-5.1.0-Linux-x86_64.sh

# 安装
sh Anaconda3-5.1.0-Linux-x86_64.sh

# 设置PATH
vim /etc/profile

# 输入以下内容
	PATH=/root/anaconda3/bin:$PATH

# 启动配置
source /etc/profile

# 安装包
pip install shadowsocks

# 配置
mkdir /etc/shadowsocks
vim /etc/shadowsocks/config.json
# 输入以下内容
{
    "server":"0.0.0.0",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"egsdf35bd",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
# 启动
ssserver -c /etc/shadowsocks/config.json -d start

# 问题
对于 ubuntu, 如果报错 undefined symbol: EVP_CIPHER_CTX_cleanup, 则
	vim /root/anaconda3/lib/python3.6/site-packages/shadowsocks/crypto/openssl.py
	将所有的 EVP_CIPHER_CTX_cleanup 替换成 EVE_CIPHER_CTX_reset

```

