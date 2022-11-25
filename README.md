# README

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...

# 创建docker数据库
`
docker run -d      --name db-for-mangosteen      -e POSTGRES_USER=mangosteen      -e POSTGRES_PASSWORD=123456      -e POSTGRES_DB=mangosteen_dev      -e PGDATA=/var/lib/postgresql/data/pgdata      -v mangosteen-data:/var/lib/postgresql/data      --network=network1  -p 5432:5432    postgres:14
`

# 创建数据模型
`bash
  bin/rails g model user email: stirng name: string
`

# 模型同步数据库
`
  bin/rails db:migrate
`

# 回滚同步操作
step: 回滚步数
`
  bin/rails  db:rollback step=n
`

# 创建controller 
`
  bin/rails g controller users create list
`

# 创建 验证码表
`
  bin/rails g model ValidationCode email:string type:string code:string used_at:datetime
`
  同步数据库
`
  bin/rails db:migrate
`
# 创建 验证码controller 
`
  bin/rails g controller validation_codes create
` 

# 创建 items controller
`
  bin/rails g controller Api::V1::Items
`
# 创建 item model
`
  bin/rails g model item user_id:bigint amount:bigint note:text tags_id:bigint happen:datetime 
  bin/rails db:migrate
`
# 初始化测试框架
提前安装测试包, 创建帮助文件
`
  bin/rails generate rspec:install
`

# 初始化测试 model user
`
 bin/rails generate rspec:model user
`

# 切换测试环境创建数据库
创建数据库
`
  RAILS_ENV=test bin/rails db:create
`
创建表
`
  RAILS_ENV=test bin/rails db:migrate
`

# 执行测试用例
`
  bundle exec rspec
`

# 创建 items 的测试

```
  bin/rails generate rspec:request items
```

# 创建  validation_codes  的测试rspec
```
  bin/rails generate rspec:request validation_codes
```

#  type 为保留字段 需要更改数据库的字段名称

```
  rails g migration ChangeTypeTokindFromValidationCode 
```
修改字段名改成了删除字段名了

# 增加字段
```
rails g migration AddKindToValidation kind:string 
```
kind:string  可以不写 ，在文件里自己加

# 增加一个首页的controller
```
  bin/rails generate controller home index
```

# 管理开发环境密钥
```
bin/rails credentials:edit 
or
EDITOR="code --wait" bin/rails credentials:edit
```
----
生成的master.key 是不能泄漏的，如果想要再次修改的话，还是执行上面文件，执行过程中，会把master.key解密为之前添加的keys

## 查看密钥
```
# 进入ruby 控制台 development
bin/rails console

# 查看全部的内容
Rails.application.credentials.config

# 查看某个
Rails.application.credentials.keyname
```

# 管理生产环境的密钥
## 生产环境的密钥
```
bin/rails credentials:edit --environment production
or
EDITOR="code --wait" bin/rails credentials:edit  --environment production

```
## 进入ruby production环境 控制台
RAILS_ENV=production bin/rails console

## 查看全部的内容
Rails.application.credentials.config

## 查看某个
Rails.application.credentials.keyname
```
⚠️ 可以有不同环境的keys,但是keys合集必须保持一致

# 本地部署
## 打包
1. 执行 pack_for_host.sh
2. 在本地找到打包的文件，执行其中的setup_host.sh
需要添加参数
```
DB_HOST=xxx PASSWORD=xxx RAILS_MASTER_KEY=xxx ./setup.sh
```


# 添加邮箱功能
```
bin/rails generate mailer User
```
## 添加邮件模版
/app/views/user_mailer/welcome_email.html.erb
```html
<!DOCTYPE html>
<html>
  <head>
    <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
  </head>
  <body>
    <h1>Welcome to example.com, <%= @user.name %></h1>
    <p>
      You have successfully signed up to example.com,
      your username is: <%= @user.login %>.<br>
    </p>
    <p>
      To login to the site, just follow this link: <%= @url %>.
    </p>
    <p>Thanks for joining and have a great day!</p>
  </body>
</html>

```
在config/environments/development.rb 配置
讲密码设置在密钥管理文件中在这里直接调用
```
config.action_mailer.perform_caching = true
config.action_mailer.delivery_method = :smtp
config.action_mailer.smtp_settings = {
  address: 'smtp.gmail.com',
  port: 587,
  domain: 'example.com',
  user_name: '<username>',
  password: Rails.application.credentials.email_password,,
  authentication: 'plain',
  enable_starttls_auto: true,
  open_timeout: 5,
  read_timeout: 5 
}
```

在console 控制台中输入UserMailer.welcome_email('12345x').deliver 测试


# 接口文档生成
https://github.com/zipmark/rspec_api_documentation

```
bin/rake docs:generate
```

# 清空一张表
进入控制台
```
  ValidationCode.destroy_all # 好像没有用
```

# 创建session的controller

```
  bin/rails generate controller api/v1/sessions_controller
```

# ruby语法

if x
  p 'x'
end

等价于

return p 'x' if x
return p 'x' if not !x
return p 'x' unless !x

# 创建tag model 

```
bin/rails generate model tag user:references name:string sign:string deleteed_at:datetime
```

---
修改migrate
不为空： null: false
不为主键： foreign_key: false

```
class CreateTags < ActiveRecord::Migration[7.0]
  def change
    create_table :tags do |t|
      t.references :user, null: false, foreign_key: false
      t.string :name, null: false, 
      t.string :sign, null: false, 
      t.datetime :deleteed_at

      t.timestamps
    end
  end
end
```
## 更新数据库
```
bin/rails db:migrate
```

# 创建tags controller

```
bin/rails generate controller api/v1/tags_controller
```