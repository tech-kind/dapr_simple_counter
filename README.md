# DaprSimpleCounter

* Daprの状態管理を利用したサンプルプログラム

## 前提条件

* 以下Daprのコマンドを実行する際は、DaprSimpleCounterディレクトリに移動して、実行する。

``` bash
cd .\DaprSimpleCounter\
```

## シンプルな実行方法

* オプションを利用しないシンプルな実行方法

``` bash
dapr run --app-id DaprCounter dotnet run
```

## 構成ファイルを読み込む

* 構成ファイルを指定して実行する方法

``` bash
dapr run --app-id DaprCounter --components-path ../dapr/components dotnet run
```

* 構成ファイルの中身

``` yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
  - name: actorStateStore
    value: "true"
scopes:
  - DaprCounter
```

* scopesのIDはDaprコマンド実行時の`-app-id`で指定するIDと一致するようにしておく
* 一致するIDが見つからない場合、実行時にエラーとなる

## DaprのHTTPポートを指定する

* DaprのHTTPポートを指定して実行する場合

``` bash
dapr run --app-id DaprCounter --dapr-http-port 3500 dotnet run
```

* HTTPポートを指定することで、指定したポートを介してDaprサイドカーと対話ができる

* 例えば、上記コマンド実行時に、別のコンソールから下記コマンドを実行する

``` bash
curl http://localhost:3500/v1.0/state/state/store/counter
```

* 正常に機能していれば、以下のようなレスポンスが返ってくることが確認できる

```
StatusCode        : 200
StatusDescription : OK
Content           : 973
RawContent        : HTTP/1.1 200 OK
                    Traceparent: 00-04d28fe2eb34851302907d45ad9ef036-68c41f9cc829b039-01
                    Content-Length: 3
                    Content-Type: application/json
                    Date: Tue, 02 Aug 2022 06:29:12 GMT
                    ETag: 973
                    Server: fastht...
Forms             : {}
Headers           : {[Traceparent, 00-04d28fe2eb34851302907d45ad9ef036-68c41f9cc829b039-01], [Content-Length, 3], [Content-Type, application/json], [Date, Tue, 02 Aug 2022 06:29:12 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : System.__ComObject
RawContentLength  : 3
```