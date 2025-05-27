# `trurl` コマンド

URL を操作する。

## インストール

```sh
git clone --depth 1 -b trurl-0.16.1 https://github.com/curl/trurl.git
cd trurl/
make
make install
```

## コマンド

JSON 形式で出力する。

```sh
> trurl --json 'http://usr:pass@localhost:8080/index.html?q=value'
[
  {
    "url": "http://usr:pass@localhost:8080/index.html?q=value",
    "parts": {
      "scheme": "http",
      "user": "usr",
      "password": "pass",
      "host": "localhost",
      "port": "8080",
      "path": "/index.html",
      "query": "q=value"
    },
    "params": [
      {
        "key": "q",
        "value": "value"
      }
    ]
  }
]
```

コンポーネントを取得する。

```sh
> trurl --get '{host} {scheme}' 'http://usr:pass@localhost:8080/index.html?q=value'
localhost http
```

コンポーネントを更新する。

```sh
> trurl --set host=127.0.0.1 'http://usr:pass@localhost:8080/index.html?q=value'
http://usr:pass@127.0.0.1:8080/index.html?q=value
```

コンポーネントに追加する。

```sh
> trurl --append path=index.html 'http://localhost/dir'
http://localhost/dir/index.html
```

ポート番号を操作する。

```sh
# well-known ポートは省略される。
> trurl 'http://localhost:80/'
http://localhost/

# well-known ポートは維持する。
> trurl --keep-port 'http://localhost:80/'
http://localhost:80/

# well-known ポートを追加する。
> trurl --default-port 'http://localhost/'
http://localhost:80/
```

クエリを操作する。

```sh
# クエリを追加する。
> trurl --append query=q=value 'http://usr:pass@localhost:8080/index.html'
http://usr:pass@localhost:8080/index.html?q=value

# クエリを取得する。
> trurl --get '{query:q}' 'http://usr:pass@localhost:8080/index.html?q=value'
value

# クエリを更新する。
> trurl --replace q=changed 'http://usr:pass@localhost:8080/index.html?q=value'
http://usr:pass@localhost:8080/index.html?q=changed

# クエリを削除する。
> trurl --qtrim q 'http://usr:pass@localhost:8080/index.html?q=value'
http://usr:pass@localhost:8080/index.html
```

標準入力から読み取る。

```sh
> echo 'http://localhost/' | trurl --url-file -
http://localhost/
```
