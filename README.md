# rails6-sample

Ruby3 × Rails6 × Nginxで動く簡単なサンプル。

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
