# Advanced Rails101

## Table of contents

* [Models](#models)
	* [Associations](#associations)
* [Migrations](#migrations)
	* [Generator syntax](#generator-syntax-2)
	* [Datatypes](#datatypes)
	* [Anatomy of a migration](#anatomy-of-a-migration)
	* [Running migrations](#running-migrations)
* [Active Record](#active-record)
	* [scopes](#scopes)
	* [dirty methods](#dirty-methods)
* [Controllers](#controllers)
	* [Strong parameters](#strong-parameters)
	* [Actionhooks](#actionhooks)	
* [Basic routing](#basic-routing) 
	* [Personalized actions](#personalized-actions) 
* [User management](#user-management)
* [Debugging](#debugging)
	* [pry](#pry)
	* [byebug](#byebug)
* [Exercises](https://github.com/hackerschoolmty/Rails101/blob/master/03/exercises.md)

## Models

### Associations

#### ```has_one```

A one-to-one association

```
invoices:
  id:integer
  order_id:integer
  #...

orders:
  id:integer
  name:string
  #...
```

```ruby
class Invoice < ActiveRecord::Base
  belongs_to :office
end

class Order < ActiveRecord::Base
  has_one :invoice
end
```

The model for the table that contains the foreign key **always** has the `belongs_to` declaration 

#### ```has_many :through```

```
posts:
  id:integer
  title:string
  content:text
  author_id:integer

authors:
  id:integer
  name:string

photos:
  id:integer
  post_id:integer
```

```
class Post < ActiveRecord::Base
  belongs_to :author
  has_many :photos
end

class Author < ActiveRecord::Base
  has_many :posts
  has_many :photos, through: :posts
end
```

#### ```has_and_belongs_to_many```

This is a many-to-many relationship,  

```
users:
  id:integer
  name:string
  email:string

skills:
  id:integer
  name:string
```

Rails implements this type of relatinship by using an intermediate join table, and for that we need an especial migration: 

```bash
$ rails g migration CreateJoinTableSkillUser skill user
invoke  active_record
      create    db/migrate/20160201233736_create_join_table_skill_user.rb
```
**Sadly this only works for rails 4, rails 3.X needs another kind of sourceries** 

The former command will generate the respective migration: 

```ruby
class CreateJoinTableSkillUser < ActiveRecord::Migration
  def change
    create_join_table :skills, :users do |t|
      # t.index [:skill_id, :user_id]
      # t.index [:user_id, :skill_id]
    end
  end
end
```

Finally we'll need to add the following into our models:

```ruby
class User < ActiveRecord::Base
  has_and_belongs_to_many :skills
end


class Skill < ActiveRecord::Base
  has_and_belongs_to_many :users
end
```

And now we can do:

```
=> user = User.create(name: "Cosme Fulanito")
=> user.skills.create(name: "Kill")
=> user.skills
=> #<ActiveRecord::Associations::CollectionProxy [#<Skill id: 1, name: "Kill", ...>]>
=> skill = Skill.create(name: "dance")
=> user.skill << skill
=> user.skills
=> <ActiveRecord::Associations::CollectionProxy [#<Skill id: 1, name: "Kill", ...>, #<Skill id: 2, name: "eat", ...>]>
```

## Migrations

As we saw before Rails encourages an agile, iterative style of development. We as developers really don't expect to get everything right the first time, migrations are the representation of this. 

The purpose of migrations is to evolve the database schema of our application over time, so we don't have to write nasty sql code. 

Migrations are just ruby classes place inside `db/migrate` directory. Each migration file's name starts with a number of digits and an underscore. Those digits are the key to the migration, **because they define the sequence in which the migrations are applied**


### Generator syntax

Although you could create these migration files by hand, it's easier and less prone to error to use a rails generator. Normally you would use two different generators to create a migration.

* By creating a model, as we saw before the model generator creates a migration that creates the table associated to the model (you can skip the migration with --skip-migration).

```bash
$ rails g model Product
      invoke  active_record
      create    db/migrate/20160201210243_create_products.rb
      create    app/models/product.rb
      invoke    test_unit
      create      test/models/product_test.rb
      create      test/fixtures/products.yml
```

 * By creating a migration on its own 

```bash
$ rails g migration «migration_name» [columns optional]
```

Check the following example

```bash
$ rails g migration AddNameToProducts
      invoke  active_record
      create    db/migrate/20160201210312_add_name_to_product.rb
```

This will create an empty file waiting for us to put the schema manipulation, but we can also take an advantage of the migration name:

>If you name your migration in UpperCamelCase like: ```AddSomethingToPluralizedTableName``` or ```RemoveSomethingToPluralizedTableName``` 
> and add the desired columns like ```field_name:data_type another_field:data_type``` 
> ```add_column``` and ```remove_column``` statements will be added automatically.

So if we do this in our terminal

```bash
$ rails g migration AddPriceToProducts price:decimal
      invoke  active_record
      create    db/migrate/20160201211537_add_price_to_products.rb
```

And then we inspect the created file:

```ruby
class AddPriceToProducts < ActiveRecord::Migration
  def change
    add_column :products, :price, :decimal
  end
end
```

### Datatypes 


```
:binary 		=> Binary data
:boolean 		=> True/False 1/0
:date			=> 2015-06-03
:datetime		=> 2015-06-03 11:20:12
:decimal		=> 12.2340
:float			=> 12.333 (in binary format, something like scientific notation)
:integer		=> 1223233 -123424243
:references	=> Associations
:string			=> "Lorem ipsum dolor" (255)
:text			=> "Long string"
```

### Anatomy of a migration

The name of the class must match the portion of the filename after the version number, in the previous case the migration could be found in a file named called `XXXXXXXXXXXXX_some_name.rb. **Please note that no two migrations can contain the same name**

Migrations are subclases of `ActiveRecord::Migration`, normally it would contain `up` and `down`methods

```ruby
class SomeName < ActiveRecord::Migration
  def up
    #...
  end
  
  def down
    #...
  end
end
```

The `up` method is responsible for applying the schema changes, while the `down` method undoes those things

```ruby
class AddNameToProduct < ActiveRecord::Migration
  def up
    add_column :products, :name, :string
  end
  
  def down
    remove_column :products, :name
  end
end
``` 

But there's a bit of duplication here isn't it? You can also use `change` method instead of `up/down` methods, see the next example:

```ruby
class AddNameToProduct < ActiveRecord::Migration
  def change
    add_column :products, :name, :string
  end
end
```

Where does `down` method go? Well when using `change` Rails will detect how to automatically undo a given operation, in this example the opposite of `add_column` is clearly `remove_column`. `change` methods will work in majority of cases

There are a lot of migrations methods you can use, if you want to see a full list of migration methods check the [documentation](http://edgeguides.rubyonrails.org/active_record_migrations.html)

### Running migrations

Migrations are run using the `db:migrate` Rake task 

```bash
$ rake db:migrate
== 20160201210519 AddNameToProducts: migrating ================================
-- add_column(:products, :name, :string)
   -> 0.0016s
== 20160201210519 AddNameToProducts: migrated (0.0017s) =======================
```

This will basically run any pending migrations you have in your application. But how do Rails know which migrations are pending? Well let's dive into the internal of Rails.

Rails mantains a table called `schema_migrations`, this table has just one column, called `version` and it will have one row per successfully applied migration. So everytime you run `rake db:migrate` the task first looks for the `schema_migrations`table and if the record doesn't exist yet, it will be created. 

If you want' to undo an specific migration you can copy the version number and add it to `down` command:

```bash
$ rake db:migrate:down VERSION=20160201210519
== 20160201210519 AddNameToProducts: reverting ================================
-- remove_column(:products, :name, :string)
   -> 0.0066s
== 20160201210519 AddNameToProducts: reverted (0.0493s) =======================
```

## Active Record

### scopes

In Active Record we han have multiple clauses in just one query

```ruby
=> Product.where("status = 'ACTIVE'").order("price")
=> Product.where("pay_type = 'po'").limit(10)
```

Well having the former, we can easily have endup with comething like this: 

```ruby
=> Product.where("status = 'ACTIVE').where("pay_type = 'po'").order("price").limit(10)
=> products = Product.limit(10)
=> products.where("status = 'ACTIVE').where("pay_type = 'po'").order("price")
```

As this chain of method calls grow longer, they also become a concern, what would happen if I want to use the same query for another set of products? Do I have to duplicate my code? Hell no, once again Rails has something that definitely help us in this situations.

```ruby
class Product
 scope :active, ~>  { status: "ACTIVE" }
 scope :po_type, ~>  { pay_type: "PO" } 
 ...
end 

=> Product.active
=> Product.po_type
```
 
 Scopes are also chainable within another scopes
 
 ```ruby
 class Product
   scope :recent, ~> { where("created_at > ?", 2.days.ago) }
   scope :active, ~> { recent.where(status: "ACTIVE") }
 end
 ```
 
 Your scope can take arguments as well:
 
 ```ruby
 class Product
   scope :created_before, ->(time) { where("created_at < ?", time) }
 end
 
 => Product.created_before(2.days.ago)
 ```
   

### dirty methods

Rails has a bunch of methods that can help you identify if a model has changed or not. See the following example:

```ruby
=> person = Person.new
=> person.changed? 
=> false

=> person.first_name = "Cosme Fulanito"
=> person.changed? 
=> true

# returns a list of attributes that have changed
=> person.changed 
=> ["first_name"]

# returns a hash of the attributes that have changed with their original values
=> person.changed_attributes 
=> {"first_name"=>nil}
 
# returns a hash of changes, with the attribute names as the keys, 
# and the values will be an array of the old and new value for that field.
=> person.changes 
=> {"first_name"=>[nil, "Cosme fulanito"]}

```

You can take this one step further and track whether a particular attribute has been changed or not

```ruby
=> person = Person.new
=> person.first_name = "Cosme Fulanito"
=> person.changed? 
=> true
=> person.first_name_changed?
=> true
=> person.first_name_was
=> nil
=> person.first_name_change 
=> [nil, "Cosme Fulanito"]
# If we check the same methods on an unchanged attribute
=> person.last_name_changed?
=> false
=> person.last_name_was
=> nil
=> person.last_name_change
=> nil
=> false
```

## Controllers

### Strong parameters

Using strong parameters we can forbid parameters to be used in Active Model mass assignments until they have been whitelisted 

```ruby
class ProductsController < ActionController::Base

  def create
    Product.create(product_params)
  end
  
  private
  
    def product_params
      params.require(:product).permit(:name, :price)
    end
 end
```

`create` action will use `product_params` private method to whitelist permissible parameters. 

Check this another case:

```ruby
class UsersController < ActionController::Base

  def create
    User.create(params[:user])
  end
end
```

This will raise an ActiveModel::ForbiddenAttributes exception because it's using a direct assignment without an explicit permit step 


In case you have `accepts_nested_attributes_for`:
Another interesting cases that is good to know for your day-to-day development are:

```ruby
# Model 
class Book
  have_many :authors
  accepts_nested_attributes_for :authors, allow_destroy: true
end

# Controller
class BooksController
  def create
    Book.create(book_params)
  end
  
  private
  
    def book_params
      params.require(:book).permit(:name, authors_attributes: [:name,  :_destroy])
    end
end
``` 


### Actionhooks  

Rails has callbacks that could be executed before or after a controller action. (*Before Rails 4, they're used as `before_filter` and `after_filter`*)

#### before_action

```ruby
class ProductsController < ApplicationController
  before_action :require_login
  
  private
  
  def require_login
    unless user_logged_in?
      flash[:error] = "You must be logged dude!"
      redirect_to login_url
    end
  end
end
```

Important things to notice here:

- This callback makes a redirect request if the condition is not accomplish, so the original action **never gets invoked**. The same will happen if the actionhook uses render. 
- By default this callback will apply to all actions in `ProductsController`. 

We can modify actionhooks behaviour with `:only` and `:except` options:

```ruby
# In this case `require_login` will only apply for `create`
class ProductsController < ApplicationController
  before_action :require_login, only: :create
  ....
end
```

```ruby
# In this case `require_login` will apply for all actions, except `index`
class ProductsController < ApplicationController
  before_action :require_login, except: :index
  ...
end
```

Filters are inherited by default, so if you set a filter on ApplicationController, it will be run on every controller of your Application, the most common case for this it will be for login validations:

```ruby
class ApplicationController < ActionController::Base
  before_action :require_login
end
```

Filters can also be scheduled, so: 

```ruby
class ProductsController < ApplicationController
  before_action :require_login
  before_action :search_for_recent_products
  ...
end
```

If `require_login` makes a render or a redirect request `search_for_recent_products` method will not be run.

#### after_action


After filter have a similar behaviour than before_action. The only differences are: 

-  Since the action has already been executed they cannot stop the action from running
-  They have access to the response data that's about to be sent to the client.

They can also be used along with before filters, so hooks can be run before and after actions


## Basic Routing

### Personalized actions

Rails resources provides you with an initial set of actions, but you don't need to stop there, you can add extended resources with `member` and `collection` options

```ruby
resources :products do 
  get :archive, on: :member
  get :synchronize_with_card, on: :collection
end
```

## User management

Rails has several gems that can help us with users management. [Devise](https://github.com/plataformatec/devise) is a great example of this. 

## Debugging

Rails has a couple of methods & techinicas that can help you [debug](http://guides.rubyonrails.org/debugging_rails_applications.html) your code. But there's also exist a couple of gems that definitly will do your live easier.

### pry

You can install this gem by running:

```bash
$ gem install pry
``` 

Then add it to your gemfile and you're ready to use it! 

```ruby
class ProductsController < ApplicationController
  def show
    binding.pry
    @product = Product.new
  end
end
```
Now fire up your browser and go to the corresponding action (in this case it will be `/localhost:3000/products/1`). As soon as your application server reaches the `binding.pry` line, a "debugger shell" will be started inside the terminal where you launched your server, now you can inspect the variables or experiment with the code in the same context the server is running it 

```
    6: def show
 => 7:   binding.pry
    8:   @product = Product.find(params[:id])
    9: end
```

To leave this sell just type `quit`or `exit`.

### Byebug

This is just like `pry` but it has more sweet features, one of coolest ones is the step-to-step process:

```ruby
class ProductsController < ApplicationController
  def show
    byebug
    @product = Product.active.find(params[:id])
  end
end
```

```ruby
    6:   def show
    7:     byebug
=>  8:     @product = Product.active.find(params[:id])
    9:   end
```

If we type `next` within the shell application, we'd go deep inside rails method calls, until we finally to the model code


```ruby
   1: class Product < ActiveRecord::Base
   2:   ACTIVE = "active"
=> 3:   scope :active, -> { where(status: ACTIVE) }
   4: end
```

To resume execution of an application just type `continue`

Finally just as `pry`, you can install this gem by runnning:

```bash
$ gem install byebug
```

And then add it to your Gemfile. 

