#Advanced Rails 201

##Cheap tricks

Build rails apps with recipes
https://github.com/RailsApps/rails-composer

Write slim code instead of html
gem 'slim'

-


- Model Calbacks
- Model Dirty Methods
- Concerns
- Ruby Slim
- Eager loading
- Multitenancy
- Caching
- Redis
- Transactions

##Model Callbacks

```ruby
class Post < ActiveRecord::Base
  belongs_to :author, foreign_key: :author_id, class_name: 'User'
  
  before_create :method_name
  before_save :method_name
  before_validations :method_name
  before_update :method_name


  after_create :method_name
  after_save :method_name
  after_validations :method_name
  after_update :method_name

  before_create :method_name, if: :another_method?

  before_create :method_name, unless: :another_method?

  before_create :method_name, if: proc {}
  
end
```

### Transactions
We need to wrap a lot of changes into a transaction because if something's broken in the parent model all the information might not work

```ruby
User.transaction do
  begin
    User.create(username: 'Nemu')
  rescue Exception => e
    raise ActiveRecord::Rollback    
  end
end
```


##Dirty Methods

You probably want to know if model column change.

```ruby

  .changed?
  .changes
  .«attribute»_changed?
  .«attribute»_was

  > u = User.first
  > u.changed? #=> false
  > u.email = 'el.chapo@gmail.com'
  > u.changed? #=> true
  > u.email_was #=> admin@email.com
  > e.changes #=> {"email"=>["admin@gmail.com","elchapo@gmail.com"]}
  
```

## Scopes review
```
defaut_scope -> { … }
scope :scope_name, -> { ... }

.order('column_name ASC')
```
## Slug column

You can save friendly_id urls in this column.

## Enum with column.
Transform integer column into enumerated 

## Counter cache
It help us by adding a column that saves an association count, pretty usefull if you don't want to execute a big query 

```rails g migration AddCounterCacheToTableName counter_cache:integer```
	
	Don't forget to add the defaut value 0
	
##Concerns

Extend functionality

```
module ApiConcern
  extend ActiveSupport::Concern

  included do
    skip_before_action :verify_authenticity_token
    before_action :authenticate!
  end
  
  def method(params)
    ...
  end  
end
```


Model Concern Example:

```
module Taggable
  extend ActiveSupport::Concern

  included do
    has_many :taggings, as: :taggable, dependent: :destroy
    has_many :tags, through: :taggings 
  end

  def tag_names
    tags.map(&:name)
  end
end
```



### Steps:

```
blog_app:
	post:
		title: string
		content: text
		picture: string (carrierwave)
		slug: string		
		author_id: integer
		status:integer
		comments_count:integer
		likes_count:integer
		
	like:
		post_id:integer
		user_id:integer
	
	comment:
		post_id:integer
		author_id:integer
		subject:string
		content:text
```


Create new app with rails_composer

create a crud for the Post model
create status for draft, published, deleted posts (enum)
create the Comment model.
create the Like model
Add like button to posts#show



