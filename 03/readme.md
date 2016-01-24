# Advanced Rails101

## Table of contents

* [Active Record](#active-record)
	* [scopes](#scopes)
	* [callbacks](#callbacks)
	* [dirty methods](#dirty-methods)
	* [save, save!, create, create!](#save-save-create-create)
* [Controllers](#controllers)
	* [Strong parameters](#strong-parameters)
	* [Actionhooks](#actionhooks)	
* [User management](#user-management)
* [Debugging](#debugging)
	* [pry](#pry)
	* [byebug](#byebug)
* [Exercises!](https://github.com/hackerschoolmty/Rails101/03/exercises.md)

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
   
### callbacks

Active Record controls the life cycle of model objects through callbacks. A normal life cycle of an object would consist on:

- create an object
- modified it
- updated it
- and finally destroyed it 

Using callbacks, Active Record lets our code participate in this monitoring process, by writing code that gets invoked at any significant event in the life of an object.  

Active Record defines sixteen [callbacks](http://guides.rubyonrails.org/active_record_callbacks.html#available-callbacks) and with these callbacks we can perform validations, map column values as they pass in and out of the database, and even prevent certain operations from completing, throughout the model life cycle.


```ruby
class Post < ActiveRecord::Base
  before_validation :downcase_title
  
  def downcase_title
    self.title = title.downcase
  end
end
```

```ruby 
class Article < ActiveRecord::Base
  after_destroy :log_destroy_action
 
  def log_destroy_action
    puts 'Article destroyed'
  end
end
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

### save, save!, create, create!

There are two versions of the save and create method, that differ in the way they report errors: 

* `save` returns true if the record was saved and `nil` otherwise.* `save!` returns true if the save operation succeeded and it raises an exception otherwise if not.* `create` returns the object regardless of whether it was successfully saved. If you want to determine wheter the data was actually written, you’ll need to check the object for validation errors* `create!` returns the Active Record object on success and it raises an exception otherwise.

Active Record assumes `save` is called in thecontext of a controller’s action method and that the view code will be presenting any errors back to the end user. And for many applications, that’s the normal behaviour! 
However, if we need to save a model object in a context where we want to make sure to handle all errors, we should use `save!`.

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

