# 常用 python 包的安装

### 安装pymssql
brew install freetds@0.91
brew link --force freetds@0.91
pip install pymssql==2.1.3

### 安装 mysqldb
brew install mysql
brew unlink mysql
brew install mysql-connector-c #或者已经安装了的话  brew link mysql-connector-c
sed -i -e 's/libs="$libs -l "/libs="$libs -lmysqlclient -lssl -lcrypto"/g' /usr/local/bin/mysql_config
pip install MySQL-python
brew unlink mysql-connector-c
brew link --overwrite mysql

### 安装 impyla 教程
1.1 使用anaconda做包管理
    conda install impyla
    在运行中可能会遇到缺少thrift_sasl包的问题，用以下命令安装：
    conda install thrift_sasl
    如果在anaconda上找不到这个包，则可改用：
    conda install --channel https://conda.anaconda.org/blaze thrift_sasl
1.2 使用pip做包管理
    pip install impyla
    pip install thrift_sasl

在 windows 上
1. conda install -c conda-forge thrift_sasl==0.2.1
2. pip install impyla==0.13.8
报错问题：
TypeError: can't concat bytes to str
https://github.com/cloudera/impyla/issues/238?tdsourcetag=s_pctim_aiomsg
在thrift_sasl的__init__.py中的94行（self._trans.write(header + body)）前添加：
    if (type(body) is str):
      body = body.encode()
若是无法使用，首先删除已有包
pip uninstall thrift_sasl
pip uninstall impyla
pip uninstall thrift