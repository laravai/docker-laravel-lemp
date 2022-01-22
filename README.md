## 前提
- VSCodeをインストールする（[公式](https://azure.microsoft.com/ja-jp/products/visual-studio-code/)）
- Docker Desktop for Mac(or Windows)をインストールする（[公式](https://www.docker.com/products/docker-desktop)）

## 自分のPC上にクローンするまで
```bash
# GitLabからプロジェクトをクローン
git clone {プロジェクトURL}

# クローンしたプロジェクトフォルダに移動
cd docker-laravel-lemp
```

## 環境構築手順
以下のコマンドを順に実行して実行環境を構築する

```bash
# srcディレクトリを作成
mkdir src

# 環境変数ファイルをコピー
cp .env.example .env

# Dockerfileからイメージをビルド
docker compose build --no-cache

# コンテナを起動
# → この時点で3つのコンテナがちゃんと起動しているか確認する（GUIもしくは`docker compose ps`など）
docker compose up -d

# Laravelをインストールするため、appコンテナに入る（/docker-laravel-lempディレクトリで実行）
# → 以下のコマンドを打つと「root@~~~:/var/www/html」といったような表示に切り替わるはず（ちゃんと切り替わっていればコンテナ内部に入れている証拠）
docker compose exec app bash

# Laravelのインストール（appコンテナの中に入っている状態（=表示が「root@~~~:/var/www/html」となっている状態で実行する））
composer create-project --prefer-dist "laravel/laravel=6.*" .
```

## 動作確認
URL: http://localhost:80
※現状ホットリロードには対応していないみたい（=コードを変更したら更新ボタン押さないと画面に変更内容が反映されない）

## 備考
以下は必要に応じて使うコマンド
```bash
# コンテナを停止&破棄
docker compose down

# コンテナの実行状況を確認
docker compose ps

# appコンテナの中（=「root@~~~:/var/www/html」）から抜ける方法
Control + D（Macの場合）
```