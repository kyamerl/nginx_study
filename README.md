# nginxの勉強

## やったこと
Docker＋nginxで443ポートでのHTTPS通信ができるようにした。

### コンテナを作成する
```
docker container run -d -p 443:443 --name nginx2 nginx
```

### コンテナの中に入る
```
docker container exec -it nginx2 bash
```

### 秘密鍵を作成する
```
mkdir /etc/nginx/ssl
openssl genrsa -out /etc/nginx/ssl/server.key 2048
```

### CSR（証明書署名要求）を作成する
```
openssl req -new -key /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.csr
```

### CRT（SSLサーバ証明書）を作成する
```
openssl x509 -days 3650 -req -signkey /etc/nginx/ssl/server.key -in /etc/nginx/ssl/server.csr -out /etc/nginx/ssl/server.crt
```

### https通信用の433のポートを利用するために、defalt.comfに追記する
本リポジトリのdefalt.comfを参照

### nginxの設定を再読み込みする
```
service nginx reload
```

## 気づき
- SSLの設定方法が変わった
    - `ssl on;`はversion 1.15.0で廃止し、1.25.1で削除された([公式ドキュメントより](https://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl))
        - 1.25.1は2023/6/13にリリースされたらしい([Nginxバージョンアップ情報](https://openstandia.jp/oss_info/nginx/version/))
    - Listenディレクティブに `ssl` を足せばよい

## 便利
### dockerコンテナからホストへのコピー
```
% sudo docker ps
% sudo docker cp <コンテナID>:/etc/nginx/conf.d/default.conf ./default.conf
```
### ホストから dockerコンテナへのコピー
```
$ sudo docker cp ./default.conf <コンテナID>:/etc/nginx/conf.d/default.conf
```
