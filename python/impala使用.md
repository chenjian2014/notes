# impala使用

<p align="right">2019-11-12</p>

```python

import pandas as pd
from impala.dbapi import connect

def run_impala(config, sql):
    """
    这是一个简单的运行 sql 的函数
    config格式如下:
    {
        'type': 'impala',
        'host': '0.0.0.0',
        'port': 21050,
        'user': 'root',
        'password': 'root',
        'auth_mechanism': 'PLAIN',
    }
    sql 就是正常的 sql, 字符串类型
    如果是 select 语句, 会返回一个 DataFrame, 否则返回 空的 DataFrame
    """
    # 建连接
    conn = connect(
        host=config['host'],
        auth_mechanism=config['auth_mechanism'],
        port=config['port'],
        user=config['user'],
        password=config['password'],
        timeout=20
    )
    cursor = conn.cursor()
    # 运行 sql
    cursor.execute(sql)
    # 获取数据
    data = cursor.description
    if data is not None and hasattr(data, '__iter__'):
        names = [metadata[0] for metadata in data]
        df = pd.DataFrame.from_records(cursor.fetchall(), columns=names)
    else:
        df = pd.DataFrame()

    # 修正列名
    cols = []
    for col in df.columns:
        cols.append(col.rsplit('.', 1)[-1])
    df.columns = cols
    # 修正列类型
    for col in df.columns:
        series = df[col][df[col].notnull()]
        if not series.empty:
            if type(series.iloc[0]).__name__ == 'Decimal':
                df[col] = df[col].astype(float)
            if type(series.iloc[0]).__name__ == 'Timestamp':
                df[col] = df[col].astype(str)
    cursor.close()
    conn.close()
    return df
    
```