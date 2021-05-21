# rails6-sample-app

Ruby3 × Rails6 × Nginxで動く簡単なサンプル。

デプロイ方法などについては以下を参照。

https://qiita.com/kazama1209/items/667fbd7fcea83602ab27

## セットアップ

rails new

```
$ docker-compose run web rails new . --force --no-deps --database=mysql --skip-test --webpacker
```

Gemfileの更新に伴い再ビルド。

```
$ docker-compose build
```

「./config/puma.rb」を編集。

```
threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 }.to_i
threads threads_count, threads_count
port        ENV.fetch("PORT") { 3000 }
environment ENV.fetch("RAILS_ENV") { "development" }
plugin :tmp_restart

app_root = File.expand_path("../..", __FILE__)
bind "unix://#{app_root}/tmp/sockets/puma.sock"

stdout_redirect "#{app_root}/log/puma.stdout.log", "#{app_root}/log/puma.stderr.log", true
```

「./config/database.yml」を編集。

```
default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: password # デフォルトだと空欄になっているはず
  host: db # デフォルトだとlocalhostになっているはず

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: <%= ENV["DATABASE_NAME"] %>
  username: <%= ENV["DATABASE_USERNAME"] %>
  password: <%= ENV["DATABASE_PASSWORD"] %>
  host: <%= ENV["DATABASE_HOST"] %>
```

データベースを作成。

```
$ docker-compose run web rails db:create
```

動作確認。

```
$ docker-compose up -d
```

[http://localhost](http://localhost) にアクセスしていつもの画面が表示されればOK。

<img width="1661" alt="68747470733a2f2f71696974612d696d6167652d73746f72652e73332e61702d6e6f727468656173742d312e616d617a6f6e6177732e636f6d2f302f3638383835342f66356634346336302d613762652d346633342d616438652d3035646466613135373434312e706e67" src="https://user-images.githubusercontent.com/51913879/119179994-9c096480-baaa-11eb-9290-e366c4f6c55f.png">

