## 実行手順

以下のコマンドを実行する

```
# 環境変数ファイルをコピー
cp .env.example .env

# Dockerfileからイメージをビルド
docker compose build --no-cache

# コンテナを起動
docker compose up -d
```

---

## 動作確認
URL: http://localhost:80

---

## 備考
以下は必要に応じて使うコマンド
```
# コンテナを停止&破棄
docker compose down

# コンテナの実行状況を確認
docker compose ps
```