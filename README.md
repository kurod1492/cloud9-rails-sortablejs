# AWS Cloud9 開発環境で Rails 6 + Sortablejs を動かしてみる

## 概要

Cloud9 上で、Rails 6 のウェブアプリケーションを作成します。
アプリケーションの内容は、ユーザーを一覧表示するというものです。

そこに [SortableJS](https://github.com/SortableJS/Sortable) という Javascript ライブラリの機能を追加して、
ユーザー一覧画面でユーザーをドラッグ&ドロップで並べ替えができるようにします。
(並べ替えた結果を保存する機能はありません)

Cloud9 上に Rails 6 開発環境が構築済みとします。

## Rails アプリケーション作成

Cloud9 を起動し、ターミナル画面を開きます(Alt + T)。

sortablejs-test という Rails アプリケーションを作成します。
Webpacker のインストールも実行します。

    rails new sortablejs-test
    cd sortablejs-test

次に scaffold を使って User モデルを持つアプリケーションの雛型を作成します。
User モデルが持つ情報は、名前(文字列型)と年齢(整数型)とします。

    rails generate scaffold User name:string age:integer
    rails db:create
    rails db:migrate

Cloud9 のプレビュー機能からアクセスできるように、アクセスを許可する設定を追加します。

config/environments/development.rb の中に「config.hosts << ".amazonaws.com"」という設定を追加します。

ウィンドウ左側の rails-test ディレクトリの一覧表示の中の config/environments/development.rb ダブルクリックして開きます。
この設定ファイルの中身は、

    Rails.application.configure do
      # 設定内容
    end

という構造になっています。今回の設定を追加する場所は、end の直前でよいと思います。

「config.hosts << ".amazonaws.com"」を development.rb の end の直前に以下のような感じで追加します。

    Rails.application.configure do
      # 設定内容
      config.hosts << ".amazonaws.com"
    end

ここで一度アプリケーションの起動を確認します。
rails server コマンドを実行します。

    rails server

Cloud9 のプレビュー機能を使ってアプリ画面を表示し、/users にアクセスします。
「Users」というページが表示されたらうまくいっています。

ターミナル画面に戻って、Ctrl + C を押し、アプリケーションを終了します。

## Sortablejs のインストール

Sortablejs をインストールします。
Yarn を使います。以下のコマンドを実行します。

    yarn add sortablejs


## Javascript プログラムの作成

並べ替えをしてくれる Javascript プログラムを作成します。
app/javascript/packs の中に新しいファイルを作成し、sortable.js というファイル名にします。

sortable.js を開き、以下のコードを記述します。

    import Sortable from 'sortablejs';
    
    var el = document.getElementById('users');
    var sortable = Sortable.create(el);

このプログラムでは、Sortablejs の機能を読み込んで、
「id = "users"」が指定されている要素を並べ替え可能な要素にしています。

コードを記述したら保存します。

## ビューテンプレートの修正

並べ替えをしたい要素に「id = "users"」を付与します。
ユーザー一覧画面でユーザーの並べ替えをしたいので、
app/views/users/index.html.erb を修正します。

app/views/users/index.html.erb を開くと 14 行目あたりに <tbody> があります。
この中にユーザーが一覧表示されます。
よってこの <tbody> に付与します。

以下のように修正します。 

修正前

    <tbody>
    
修正後

    <tbody id="users">

さらに、この画面で並べ替えの Javascript プログラムを動作させたいので、Javascript プログラムを呼び出すコードを追加します。
具体的には、以下のコードを、このファイルの最下行に追加します。

    <%= javascript_pack_tag 'sortable' %>

このコードの意味は「app/javascript/packs/sortable.js を読み込む」という意味です。

追加したら保存します。

## 動作確認

ターミナル画面で rails server を実行してアプリケーションを起動します。

Cloud9 のプレビュー機能を使って、/users にアクセスします。

ユーザーを3〜4個登録して、並べ替えができることを確認します。

並べ替えた結果を保存する機能はありませんので、画面を再読み込みすると元に戻ります。

