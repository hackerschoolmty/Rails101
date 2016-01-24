# Introduction to Rails

## Table of contents

* [Slides](https://github.com/hackerschoolmty/Rails101/blob/master/01/01.pdf)
* [Rails setup](#rails-setup)
	* [Mac & Linux](#mac-and-linux)
	* [Windows](#windows)
* [Basic Rails commands](#basic-rails-commands)
	* [rails new](#rails-new)
	* [rails server](#rails-server)
	* [rails generate](#rails-generate)
	* [rails console](#rails-console)
	* [rails dbconsole](#rails-dbconsole)
	* [rake](#rake)
	* [bundle commands](#bundle-commands) 
* [Exercises](https://github.com/hackerschoolmty/Rails101/blob/master/01/exercises.md)	

## Slides

You can found the introductory slides right [here](https://github.com/hackerschoolmty/Rails101/blob/master/01/01.pdf)
 
## Rails setup

This guide assumes you already have ruby installed on your computer. If you haven't dont panic, follow any of the following instructions: 

- [Installing ruby with rbenv](https://github.com/rbenv/rbenv#installation)
- [Installing ruby with rvm](https://rvm.io)

Also make sure you're using ruby 2.2 or so before you proceed.

```bash
$ ruby -v
ruby 2.2.1p85 (2015-02-26 revision 49769) [x86_64-darwin14]
```

### Mac OS & Linux

In your terminal type: 

```bash
# This could take a while...
$ gem install rails
```

And that's it! Although its probably a good idea to verify your installation:

```bash
$ rails --version
$ Rails 4.2.5
```

### Windows

Download [RailsInstaller](http://railsinstaller.org/en)  for Ruby 2.1 and run it, after that click through the installer using the default options. Verify your installation by running the following in your command prompt

```bash
$ rails --version
$ Rails 4.2.5
```

## Basic Rails commands

The following commands are absolutely critical to your everyday usage.

* rails new *app_name*
* rails server
* rails generate 
* rails console
* rails dbconsole
* rake
* bundle install

All commands can run with `-h` or `--help` to list more information.

### rails new

This creates a new Rails application, which is probably the first thing we'll want to do after installing Rails. 

```bash
rails new hacker_store
```

The former will give you the basic structure to immediately get started. 

```bash
❯ rails new hacker_store
      create
      create  README.rdoc
      create  Rakefile
      create  config.ru
      create  .gitignore
      create  Gemfile
      create  app
      create  app/assets/javascripts/application.js
      create  app/assets/stylesheets/application.css
      create  app/assets/images/.keep
      create  app/controllers/application_controller.rb
      create  app/helpers/application_helper.rb
      create  app/views/layouts/application.html.erb
      create  app/mailers/
      create  app/models/
      ....
      create  bin
      create  bin/bundle
      create  bin/rails
      create  bin/rake
      create  bin/setup
      create  config
      create  config/routes.rb
      create  config/application.rb
      create  config/environment.rb
      create  config/secrets.yml
      create  config/environments
      ....
      create  config/initializers
      .....
      create  config/locales
      create  config/locales/en.yml
      create  config/boot.rb
      create  config/database.yml
      create  db
      create  db/seeds.rb
      create  lib
      create  lib/tasks
      ....
      create  log
      create  log/.keep
      create  public
      create  public/404.html
      create  public/422.html
      create  public/500.html
      create  public/favicon.ico
      create  public/robots.txt
      create  test/fixtures
      create  test/controllers
      create  test/mailers
      create  test/models
      create  test/helpers
      create  test/integration
      create  test/test_helper.rb
      ...
      create  tmp/cache
      ...
      create  vendor/assets/javascripts
      create  vendor/assets/stylesheets
      ...
         run  bundle install
```

You have the option to specify what kind of database and even what kind of source code management system your application is going to use! The main purpose of this is save a few minutes and many keystrokes


```bash
$ mkdir hacker_store && cd hacker_store
$ git init
Initialized empty Git repository in .git/
$ rails new . --git --database=postgresql
```

The only catch by using this options is that you have to make your application's directory first, then initialize your git repository and finally run `rails new` command


### rails server

This start the WEBrick server at port 3000. Go to your favorite browser and open `http://localhost:3000`, you will see your rails app running and responding to your requests.

You can also use the alias `s` to start the server:

```bash
$ rails s
```

For now on we will use this to start up our rails apps.

The server can be run on a different port through the `-p` option, this is very handy when you have multiple rails app running on your machine.
 

```bash
$ rails s -p 3001
=> Booting WEBrick
=> Rails 4.2.5 application starting in development on http://localhost:3001
```
After that if you go `http://localhost:3001` you will see your rails app running

The server uses development environment as a default environment but this can be changed using `-e` flag

```bash
$ rails s -e production -p 4000
=> Booting WEBrick
=> Rails 4.2.5 application starting in production on http://localhost:4000
```

Finally if you want to stop the server just go to the terminal and hit Ctrl + C.

```bash
^C
[2016-01-31 14:32:28] INFO  going to shutdown ...
[2016-01-31 14:32:28] INFO  WEBrick::HTTPServer#start done.
Exiting
```

### rails generate

This command is basically a code generator. It will create everything you need for your rails app: controllers, models, mailers, scaffolds, etc. 

Run `rails generate` with no arguments for usage information:

```bash
rails generate
Usage: rails generate GENERATOR [args] [options]

General options:
  -h, [--help]     # Print generator's options and usage
  -p, [--pretend]  # Run but do not make any changes
  -f, [--force]    # Overwrite files that already exist
  -s, [--skip]     # Skip files that already exist
  -q, [--quiet]    # Suppress status output

Please choose a generator below.

Rails:
  assets
  controller
  generator
  helper
  integration_test
  jbuilder
  job
  mailer
  migration
  model
  resource
  scaffold
  scaffold_controller
  task

Coffee:
  coffee:assets

Js:
  js:assets

TestUnit:
  test_unit:generator
  test_unit:job
  test_unit:plugin
```

Also you can use `rails generate` for information on a particular generator: 

```bash
$ rails generate migration
Usage:
  rails generate migration NAME [field[:type][:index] field[:type][:index]] [options]

Options:
      [--skip-namespace], [--no-skip-namespace]  # Skip namespace (affects only isolated applications)
  -o, --orm=NAME                                 # Orm to be invoked
                                                 # Default: active_record
...
```

You can also use the shortcut command `g`

```bash
rails g migration
Usage:
  rails generate migration NAME [field[:type][:index] field[:type][:index]] [options]

Options:
      [--skip-namespace], [--no-skip-namespace]  # Skip namespace (affects only isolated applications)
  -o, --orm=NAME                                 # Orm to be invoked
                                                 # Default: active_record
...
```

### rails destroy

This is the exact opposite of `generate`. It'll undo autogenerated files created by `generate` did:

```bash
$ rails g model bah
      invoke  active_record
      create    db/migrate/20160131205854_create_bahs.rb
      create    app/models/bah.rb
      invoke    test_unit
      create      test/models/bah_test.rb
      create      test/fixtures/bahs.yml

$ rails destroy model bah
invoke  active_record
      remove    db/migrate/20160131205854_create_bahs.rb
      remove    app/models/bah.rb
      invoke    test_unit
      remove      test/models/bah_test.rb
      remove      test/fixtures/bahs.yml
```

Just like any other rails command you can use `d` shortcut to invoke the destroy command, so the former could be also written like this:

```bash
$ rails d model bah
invoke  active_record
      remove    db/migrate/20160131205854_create_bahs.rb
      remove    app/models/bah.rb
      invoke    test_unit
      remove      test/models/bah_test.rb
      remove      test/fixtures/bahs.yml
```

### rails console

This command allows you to interact with your Rails application methods from the command line. It's basically the IRB for rails.

```bash
$ rails console
Loading development environment (Rails 4.2.5)
2.2.1 :001 >
```
You can use the `c` shortcut for the same purposes

```
$ rails c
Loading development environment (Rails 4.2.5)
2.2.1 :001 >
```

For now on we'll use this shortcut.  Just as the `rails server` command the console will run in a development environment by default, but you can also specify another environment:

```
$ rails c test
Loading test environment (Rails 4.2.5)
2.2.1 :001 >
```

If you want to test out some code **without changing any data** `--sandbox` is your friend

```
$ rails c --sandbox
Loading development environment in sandbox (Rails 4.2.5)
Any modifications you make will be rolled back on exit
2.2.1 :001 >
```

To leave the console just type `exit` or `quit`  

### rails dbconsole

This is the forgotten friend of `rails console` command, by running it, it will figure out which database your application is currently using and drops you into the corresponding command line interface.

So if we we're using a sqlite in your rails app, by running `rails dbconsole` in our terminal, we'll enter into sql command line interface:

```bash
rails dbconsole
SQLite version 3.8.10.2 2015-05-20 18:17:19
Enter ".help" for usage hints.
sqlite>
```

### rake 

Rake is a utility similar to Make in Unix, it stands for Ruby Make. In Rails, Rake is used for common administration tasks, you can get a list of Rake tasks available with `rake --tasks`

```bash
$ rake --tasks
rake about                              # List versions of all Rails frameworks and the environment
rake assets:clean[keep]                 # Remove old compiled assets
rake assets:clobber                     # Remove compiled assets
rake assets:environment                 # Load asset compile environment
rake assets:precompile                  # Compile all the assets named in config.assets.precompile
rake cache_digests:dependencies         # Lookup first-level dependencies for TEMPLATE (like messages/show or comments/_commen...
rake cache_digests:nested_dependencies  # Lookup nested dependencies for TEMPLATE (like messages/show or comments/_comment.html)
rake db:create                          # Creates the database from DATABASE_URL or config/database.yml for the current RAILS_...
rake db:drop                            # Drops the database from DATABASE_URL or config/database.yml for the current RAILS_EN...
rake db:fixtures:load                   # Load fixtures into the current environment's database
rake db:migrate                         # Migrate the database (options: VERSION=x, VERBOSE=false, SCOPE=blog)
rake db:migrate:status                  # Display status of migrations
rake db:rollback                        # Rolls the schema back to the previous version (specify steps w/ STEP=n)
rake db:schema:cache:clear              # Clear a db/schema_cache.dump file
rake db:schema:cache:dump               # Create a db/schema_cache.dump file
rake db:schema:dump                     # Create a db/schema.rb file that is portable against any DB supported by AR
rake db:schema:load                     # Load a schema.rb file into the database
rake db:seed                            # Load the seed data from db/seeds.rb
rake db:setup                           # Create the database, load the schema, and initialize with the seed data (use db:rese...
rake db:structure:dump                  # Dump the database structure to db/structure.sql
rake db:structure:load                  # Recreate the databases from the structure.sql file
rake db:version                         # Retrieves the current schema version number
rake doc:app                            # Generate docs for the app -- also available doc:rails, doc:guides (options: TEMPLATE...
rake log:clear                          # Truncates all *.log files in log/ to zero bytes (specify which logs with LOGS=test,d...
rake middleware                         # Prints out your Rack middleware stack

...
... and many many more... 
```

As you can see each task has a description, and it should help you to get going. You can also use `-T` flag to obtain the same results

#### rake about

Gives you a general overlook for your rails app, including ruby version, ruby gems, rails database adapter, schema version, etc. It is useful when you need some stats for an exiting Rails installation or to verify if your rails application has everything you need to work for

```bash
$ rake about
About your application's environment
Rails version             4.2.5
Ruby version              2.2.1-p85 (x86_64-darwin14)
RubyGems version          2.4.6
Rack version              1.6.4
JavaScript Runtime        Node.js (V8)
Middleware                Rack::Sendfile, ActionDispatch::Static, Rack::Lock, #<ActiveSupport::Cache::Strategy::LocalCache::Middleware:0x007fe0941387c0>, Rack::Runtime, Rack::MethodOverride, ActionDispatch::RequestId, Rails::Rack::Logger, ActionDispatch::ShowExceptions, WebConsole::Middleware, ActionDispatch::DebugExceptions, ActionDispatch::RemoteIp, ActionDispatch::Reloader, ActionDispatch::Callbacks, ActiveRecord::Migration::CheckPending, ActiveRecord::ConnectionAdapters::ConnectionManagement, ActiveRecord::QueryCache, ActionDispatch::Cookies, ActionDispatch::Session::CookieStore, ActionDispatch::Flash, ActionDispatch::ParamsParser, Rack::Head, Rack::ConditionalGet, Rack::ETag
Application root          /Users/MayraCabrera/hacker_store
Environment               development
Database adapter          sqlite3
Database schema version   0
```

### rake db

This is the most common task of Rake, particulary `migrate` and `create` methods. Rails encourages agile practices, and that applies to our database schema, as it constantly evolves with each step of our application's code. With rails each of those steps are made possible through the use of migrations. Migrations we'll cover in [this section](https://github.com/hackerschoolmty/Rails101/tree/master/02#migrations) 

#### rake routes

This will show a complete list of available routes in your application, which is a useful overlook of the URL's in an application

```bash
$ rake routes
Prefix Verb   URI Pattern                  Controller#Action
    products GET    /products(.:format)          products#index
             POST   /products(.:format)          products#create
 new_product GET    /products/new(.:format)      products#new
edit_product GET    /products/:id/edit(.:format) products#edit
     product GET    /products/:id(.:format)      products#show
             PATCH  /products/:id(.:format)      products#update
             PUT    /products/:id(.:format)      products#update
             DELETE /products/:id(.:format)      products#destroy
  home_hello GET    /home/hello(.:format)        home#hello
home_goodbye GET    /home/goodbye(.:format)      home#goodbye
```
#### rake stats
We might be interested to see how much code we've written. And guess what?? There's a task for that:

```bash
$ rake stats
+----------------------+-------+-------+---------+---------+-----+-------+
| Name                 | Lines |   LOC | Classes | Methods | M/C | LOC/M |
+----------------------+-------+-------+---------+---------+-----+-------+
| Controllers          |     5 |     3 |       1 |       0 |   0 |     0 |
| Helpers              |     2 |     2 |       0 |       0 |   0 |     0 |
| Models               |     0 |     0 |       0 |       0 |   0 |     0 |
| Mailers              |     0 |     0 |       0 |       0 |   0 |     0 |
| Javascripts          |    16 |     0 |       0 |       0 |   0 |     0 |
| Libraries            |     0 |     0 |       0 |       0 |   0 |     0 |
| Controller tests     |     0 |     0 |       0 |       0 |   0 |     0 |
| Helper tests         |     0 |     0 |       0 |       0 |   0 |     0 |
| Model tests          |     0 |     0 |       0 |       0 |   0 |     0 |
| Mailer tests         |     0 |     0 |       0 |       0 |   0 |     0 |
| Integration tests    |     0 |     0 |       0 |       0 |   0 |     0 |
+----------------------+-------+-------+---------+---------+-----+-------+
| Total                |    23 |     5 |       1 |       0 |   0 |     0 |
+----------------------+-------+-------+---------+---------+-----+-------+
  Code LOC: 5     Test LOC: 0     Code to Test Ratio: 1:0.0
```

### bundler

Lets talk about [bundler](http://bundler.io)! If you're developing as a part of your team, you probably want every member of the team to use the same version of dependencies. The same happens when you deploy, you want to ensure that the version of the dependencies that you developed with are the ones actually used in production. Welll bundler take care of this for you! 

Bundler uses `Gemfile` to list all dependencies used in your application, a `Gemfile` for a new rails app would look something like this:

 *Note that we're using rails 4, if you're using a different version of rails, Gemfile would look a bit different. Also comments were removed for reading purposes*

```ruby
source 'https://rubygems.org'

gem 'rails', '4.2.5'
gem 'sqlite3'
gem 'sass-rails', '~> 5.0'
gem 'uglifier', '>= 1.3.0'
gem 'coffee-rails', '~> 4.1.0'

gem 'jquery-rails'
gem 'turbolinks'
gem 'jbuilder', '~> 2.0'
gem 'sdoc', '~> 0.4.0', group: :doc
gem 'redcarpet', '~> 3.1.2'

group :development, :test do
  gem 'byebug'
end

group :development do
  gem 'web-console', '~> 2.0'
  gem 'spring'
end
```

Some things to notice here:

* First line `source 'https://rubygems.org'` just specifies where to find new gems and new version of existing gems. 
* `gem 'rails', '4.2.5'` specifies the rails version to use in the app
* The remaining lines list a few gems that you might consider using
* Some gems are placed in in groups named `:development`, `:test` or `:production` and only be available on those environments

#### Basic workflow

1. Specify your dependencies in a Gemfile project root
2. Install all of the required gems through `bundle install`
3. Thats it! 


Later if you want to update a new gem you can use `bundle update <gem_name>`, e.g: `bundle update devise` this will update `devise` gem to his latest version without updating any other gems.
 
#### Bundler Operators

Gemfile supports different operators but two are the most commonly used (In the previous `Gemfile` you might have catched them):

* `>=` - This basically means "greater or equal than". So in this case `gem 'uglifier', '>= 1.3.0'`, we can use any version `uglifier` that is greather or equal to `1.3.0` 
* `~>` - This one is more widely recommended. Essentially all the parts of the version, with the exception of the last part, must be matched exactly. So `~> 3.1.4` matches any version that starts with `3.1` and is not less than `3.1.4`. Similarly `~> 2.1` means any version string that starts with `2.1` and ends `3.0` 


#### Gemfile.lock

This is the companion file of Gemfile. This file ensures that other developers in your app use the exact same dependencies as you just installed. For this reason **it's important that this files gets checked into your version control system** because it will ensure that your developing team and deployment targets will be use the same configuration. 
