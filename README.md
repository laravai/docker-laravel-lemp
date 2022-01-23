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

=========================
# このタイミングでこれから使うものがちゃんとあるか一応確認
# Composerの確認 -> Composer version 2.0.14 2021-05-21 17:03:37
composer -v

# npmの確認 -> 6.14.6
npm -v
=========================

# Laravelのインストール（appコンテナの中に入っている状態（=表示が「root@~~~:/var/www/html」となっている状態で実行する））
composer create-project --prefer-dist "laravel/laravel=6.*" .
```

## 動作確認
URL: http://localhost:80
※現状`*.blade.php`ファイルはホットリロードには対応していないみたい（=コードを変更したら更新ボタン押さないと画面に変更内容が反映されない）

## ホットリロードを設定したい場合
Dockerのappコンテナ内で以下の作業を実行する
（※ローカルにNodeが入っている場合はローカルから入れてしまってもコンテナ内に同期されるのでどちらもでよい）
（※後で判明。恐らくこれローカル環境にNode.jsが入ってないと動かせないかも…？）
```bash
# 前提
現在ターミナルの表示が「root@~~~:/var/www/html」となっていて、ちゃんとappコンテナの中に入れていることを確認する
現状コンテナ内のこの場所がローカルの`/src`ディレクトリとリンクしている（Laravelがインストールしてある場所）

# 「root@~~~:/var/www/html（=/srcとリンクしている）」で以下のコマンドを打って必要パッケージをインストール
npm install browser-sync browser-sync-webpack-plugin


========== appコンテナ内の作業はここで終わり！これ以降は/srcディレクトリ内で作業！ ==========


# src/webpack.mix.jsを以下のように編集
mix.js('resources/js/app.js', 'public/js')
    .sass('resources/sass/app.scss', 'public/css')
    .browserSync({
        files: ["resources/**/*", "public/**/*"],
        proxy: {
            target: "http://127.0.0.1:80",
        },
    });

# ホットリロード起動（/srcディレクトリで実行）
# ※この時Dockerのwebコンテナも立ち上げ済みであること（docker compose up -d）
npm run watch

# ホットリロードに対応した画面を表示する（恐らく上記コマンドで勝手に開くはず）
http://localhost:3000

# ここまでで上手く行かなかった場合、Docker内のappコンテナ内に入って`npm run watch`コマンドを実行してみると直るかも？
→ Dockerコンテナ内からブラウザの localhost:3000 にアクセスできない？みたいでダメそう。
・Nodeインストール（Mac）：https://volta.sh/
・Nodeインストール（Windows）：https://qiita.com/echolimitless/items/83f8658cf855de04b9ce
```

## その他
以下は必要に応じて使うコマンド
```bash
# コンテナを停止&破棄
docker compose down

# コンテナの実行状況を確認
docker compose ps

# appコンテナの中（=「root@~~~:/var/www/html」）から抜ける方法
`exit`と打つ。もしくは、`Control+D`を入力（Macの場合）

# 全てやり直したい場合
/srcディレクトリを丸ごと削除する → その後ルートディレクトリで手順1の `mkdir src` からやり直そう！
```