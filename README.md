## 前提
- Docker Desktop for Mac(or Windows)をインストールする（→ [公式](https://www.docker.com/products/docker-desktop)）

## 自分のPC上にクローンするまで
```bash
# GitLabからプロジェクトをクローン
git clone {プロジェクトURL}
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
docker compose up -d
```

## 動作確認
URL: http://localhost:80

## 備考
以下は必要に応じて使うコマンド
```bash
# コンテナを停止&破棄
docker compose down

# コンテナの実行状況を確認
docker compose ps
```