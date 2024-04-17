# Docker

## Dockerとは
Dockerは、「**コンテナ型の仮想環境を作成、共有、実行するためのプラットフォーム**」です。

### コンテナ is 何？

**コンテナとは**
* Dockerエンジンの上に立つ一つの箱
* 箱の中には特定したもの
* コンテナに何を入れるか、を考える

### 簡単に言うと、マシン内の入れ物に任意のものを入れられる便利な箱！！

## Dockerfile
DockerfikeとはDockerイメージを構築するための設計図のようなものです。つまり、Dockerfileを編集することで環境構築できるようになります。

**一つ一つのコンテナの詳細が書かれたファイル**

### Dockerfileの構文
Dockerfileは命令の引数により構成されます。一行につき一つの命令が与えられ実行する際に一行目から順番で実行されます。

``
{命令} {引数}
``

以下が命令の一例です。

| 命令 | 使用用途 |
|:---- |:------- |
|FROM |ベースイメージを指定する|
|RUN |コマンドを実行する|
|CMD |コンテナの実行コマンド|
|ENV |環境変数の指定|
|COPY |ファイルコピー|

## Docker Compose
### Docker Composeの役割
Dockerを利用した開発では複数のコンテナを起動して、コンテナ間の通信を行う必要があります。
今回はRailsを動かすコンテナとMySQL(DB)を動かすコンテナを起動します。いちいち手動でコンテナの起動などを行うのは連携するコンテナが増えると面倒になってきます。その手間を解消するために必要となるのが、**Docker Compose**です。

Docker Composeでは、**compose.yml**と呼ばれるDockerに対する指示書を作成します。Dockerはこの指示書に基づいて複数のコンテナを同時に起動します。
**複数のコンテナを管理するファイル**

### YMLファイルの構文
`db`という欄でMySQLのコンテナの起動に関する設定をし、`web`という欄でRailsサービス起動に関する設定をしています。
この`db`という名前で、後ほどRailsからMySQLにアクセスするための設定をしていきます。

### Gemfile
```:Gemfile
source 'https://rubygems.org'
gem 'rails', '~> 7.0'
```
Railsのバージョンが7であることを指定できました。

### entrypoint.sh の編集
このファイルは、コンテナ起動時に実行するスクリプトを記述するためのファイルです。

```bash:entrypoint.sh
#!/bin/bash
set -e

# Remove a potentially pre-existing server.pid for Rails.
rm -f /sample_rails/tmp/pids/server.pid

# Then exec the container's main process (what's set as CMD in the Dockerfile).
exec "$@"
```
ここではRailsでよくある`server.pid`のエラーを回避するために、一度`server.pid`を削除しています。

また、最後の`exec "$@"`は、コンテナ起動時に実行するコマンドを指定するためのものです。
これにより、Dockerfileで指定した`CMD ["rails", "server", "-b", "0.0.0.0"]`が実行されます。

これでスタート時点で必要なファイルは準備できました！
