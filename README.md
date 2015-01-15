# SixArm » Rails » <br>
Demo Devise authentication

Rails demonstration application that shows how to set up the Devise gem for user authentication.


## Start

Create a new Rails application:

```sh
rails new demo_devise --database=postgresql --skip-bundle
cd demo_devise
```

Bundle:

```sh
bundle install --path vendor/bundle --binstubs vendor/bundle/binstubs
echo /vendor >> .gitignore
```

Initialize the database:

```sh
bin/rake db:create db:migrate
```

Test:

```sh
bin/rake test
```


## Launch

Start the Rails server:

```sh
bin/rails server
```

Browse http://localhost:3000 and you see the application running.


## Home Page

Edit `config/routes.rb` and change the root path to this:

```ruby
root 'home#index'
```

Generate:

```sh
bin/rails generate controller home index
```

Browse `http://localhost:3000` to see the new home page.


## Messages

Edit `app/views/layouts/application.html.erb` and insert these lines after the body tag:

```erb
<body>
  <p class="notice"><%= notice %></p>
  <p class="alert"><%= alert %></p>
```


## Mailers

Edit `config/environments/development.rb` and add this:

```ruby
# Set a default host that will be used in all mailers.
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

Do the same for `test.rb` and `production.rb` for now; you will need to change these later.


## Generate User

Generate a user:

```sh
rails generate resource user name:string
bin/rake db:migrate
```


## Devise

Edit `Gemfile`:

```ruby
# Use Devise for user authentication
gem 'devise'
```

Bundle:
```sh
bundle
```

Verify `config/environments/development.rb` sets the mailer default, such as:

```ruby
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

Verify `app/views/layouts/application.html.erb` shows messges, such as:

```erb
<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>
```

Verify `config/routes.rb` has a root line, such as:

```ruby
root 'home#index'
```

Generate:

```sh
rails generate devise:install
rails generate devise user
bin/rake db:migrate
```

Edit `test/fixtures/users.yml` and add a unique email address to each user:

```yaml
one:
  name: MyString
  email: user1@example.com
two:
  name: MyString
  email: user2@example.com
```


## Devise upgrades

Devise upgrades the application and user model with these capabilities:

  * email
  * encrypted password
  * sign in tracking
  * confirmable accounts
  * lockable accounts

The `db/schema.rb` users section looks like this:

```ruby
create_table "users", force: :cascade do |t|
  t.string   "name"
  t.datetime "created_at",                          null: false
  t.datetime "updated_at",                          null: false
  t.string   "email",                  default: "", null: false
  t.string   "encrypted_password",     default: "", null: false
  t.string   "reset_password_token"
  t.datetime "reset_password_sent_at"
  t.datetime "remember_created_at"
  t.integer  "sign_in_count",          default: 0,  null: false
  t.datetime "current_sign_in_at"
  t.datetime "last_sign_in_at"
  t.inet     "current_sign_in_ip"
  t.inet     "last_sign_in_ip"
end

add_index "users", ["email"], name: "index_users_on_email", unique: true, using: :btree
add_index "users", ["reset_password_token"], name: "index_users_on_reset_password_token", unique: true, using: :btree
```


## Devise authentication

Edit `app/controllers/home_controller.rb` and add authentication:

```ruby
class HomeController < ApplicationController
  before_action :authenticate_user!
```

Browse `http://localhost:3000/` and you now see the Devise sign in page.

The page says "You need to sign in or sign up before continuing."


## Devise sign up

Sign up:

  * Email: `alice@example.com`
  * Password: `secretive`

You should see: "Welcome! You have signed up successfully."

To see the new user record in Rails:

```sh
bin/rails console
```

```irb
> User.first.email
  User Load (0.7ms)  SELECT  "users".* FROM "users"  ORDER BY "users"."id" ASC LIMIT 1
=> "alice@example.com"
```
