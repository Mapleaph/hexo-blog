title: Setting up Redmine
date: 2015-11-21 22:16:41
categories: Techniques
---

## 安装 Ruby on Rails
### 安装 RVM
``` bash
# for single user, it will be installed under ~/.rvm
$ curl -L get.rvm.io | bash -s stable
# for all users, it will be installed under /usr/local/rvm
$ sudo curl -L get.vm.io | bash -s stable
```

### 安装 Ruby
``` bash
$ rvm install ruby
# check the ruby version
$ ruby -v
```

### 安装 Rails
``` bash
# list all the download sources
$ gem source -l
# remove the origin sources
$ gem source -r https://rubygems.org/
$ gem source -a https://ruby.taobao.org/
$ gem install rails
```

## 安装 Apache2
``` bash
$ sudo apt-get install apache2
```

## 安装 MySQL, PhpMyAdmin
``` bash
$ sudo apt-get install mysql-client mysql-server phpmyadmin
```

## 安装 PHP
``` bash
$ sudo apt-get install libapache2-mod-php5
```

## 安装 Redmine

Download the source from www.redmine.org and extract the file to /var/www/html/redmine.
**Make sure that the redmine directory belongs to group www-data.**
**The own of the redmine directory should in the group of www-data.**

``` bash
$ cd /var/www/html
$ sudo chown -R www-data:www-data redmine
$ cd redmine
$ sudo chmod -R 775 files log tmp public/plugin_assets
# if you set the permission to 755 according to the official doc,
# you will not install the plugin successfully.
```

### 创建数据库
``` mysql
CREATE DATABASE redmine CHARACTER SET utf8;
CREATE USER 'redmine'@'localhost' IDENTIFIED BY '<my_password>';
GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
```

### 数据库连接配置
``` bash
$ cd /var/www/html/redmine/config
$ copy database.yml.example database.yml
$ vim database.yml

production:
  adapter: mysql2
  database: redmine
  host: localhost
  username: redmine
  password: <my_password>
```

### 安装依赖
``` bash
$ cd /var/www/html/redmine
$ gem install bundler
$ bundle install --without development test
# during the process, you may be prompted to install Rmagick
```

### 生成 session secret key
``` bash
$ cd /var/www/html/redmine
$ bundle exec rake generate_secret_token
# or
$ rake secret
# then we should add that key to the /etc/profile file like this
export SECRET_KEY_BASE=asdf12320890asd09f23rlkuf09da09sdfklk324
```

### 初始化数据库

**该过程可以在你删除了数据库中的表之后新建数据库用**

``` bash
$ cd /var/www/html/redmine
$ RAILS_ENV=production bundle exec rake db:migrate
$ RAILS_ENV=production bundle exec rake redmine:load_default_data
```

### 测试
``` bash
$ cd /var/www/html/redmine
$ bundle exec rails server webrick -e production [-b <ip>] [-d]
# the -b option change the default address from localhost:3000
# to <ip>:3000
# the -d option runs the server as a daemon.
```

## 建立 Apache2 和 Redmine 的连接

``` bash
$ gem install passenger
$ passenger-install-apache2-module
# then the install process will guide you through.
# during the process, you will be asked to add code like the following to /etc/apache2/apache2.conf
LoadModule passenger_module /usr/local/rvm/gems/ruby-2.2.1/gems/passenger-5.0.21/buildout/apache2/mod_passenger.so
<IfModule mod_passenger.c>
        PassengerRoot /usr/local/rvm/gems/ruby-2.2.1/gems/passenger-5.0.21
        PassengerDefaultRuby /usr/local/rvm/gems/ruby-2.2.1/wrappers/ruby
</IfModule>
```

### 建立站点配置

``` bash
$ sudo vim /etc/apache2/apache2.conf
#add 'ServerName localhost'
```

``` bash
$ sudo vim /etc/apache2/sites-available/000-default.conf

# the default port is for redmine
<VirtualHost *:80>
        DocumentRoot /var/www/html/redmine/public
        <Directory /var/www/html/redmine/public>
                AllowOverride All
                Options -MultiViews
        </Directory>
        RailsEnv production
        RailsBaseURI /redmine
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

Listen 3000 # we should listen to port 3000, or we cannot connect to it
# this is for the original apache site
<VirtualHost *:3000>
        DocumentRoot /var/www/html/
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## 配置 Redmine Mail 功能

1. 申请邮箱
2. 编辑配置文件

    ``` bash
    $ cd /var/www/html/redmine/config
    $ copy configuration.yml.example configuration.yml
    $ vim configuration.yml
    # add the following to the last section of the file    
    # be careful that this configuration cannot accept tab, we should all use spaces instead.
    production:
      email_delivery:
        delivery_method: :smtp
        smtp_settings:
          address: "smtp.sohu.com"
          port: 25
        # if the site supports ssl access, then we should disable the port 25
        # then uncomment the following three lines.
        # tls: true
        # enable_starttls_auto: true
        # port: 587
          domain: "smtp.sohu.com"
          authentication: :login
          user_name: "lhserver2011@sohu.com"
          password: 123456 # password don't quoted
    ```

## 建立 Redmine 同 Git 的连接

假设 git 的某个工程存放在 /home/git/repositories/xyz.git，那么在 Redmine 中建立工程的版本库时，就要写这个绝对路径。

``` bash
# 将 www-data 加入到 git 组中
$ sudo adduser www-data git
# 更正 xyz.git 的权限为 755
```

## 添加 DMSF 插件
``` bash
$ cd /var/www/html/redmine
$ git clone https://github.com/danmunn/redmine_dmsf.git plugins/redmine_dmsf
$ bundle exec rake redmine:plugins:migrate RAILS_ENV="production"
$ sudo service apache2 restart
# then in the redmine project page, you should manually check the box of DMSF
```

## 查看日志
``` bash
$ tail -f /var/log/apache2/error.log
```
