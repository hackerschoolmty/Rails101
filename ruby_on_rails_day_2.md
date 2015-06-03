===
#Ruby on Rails Day 2

- Migrations
- Data types
- ActiveRecord
- Validations
- Associations 
- ActiveRecord Query Interface
- Scopes

===

##Migrations

- Migrations allow you to evolve your database schema over time.
- You don't have to write SQL.
- Structured way to version your database schema.

####Generator syntax:
```
rails generate migration «migration_name» [columns optional]
```

or

```
rails g migration «migration_name» [columns optional]
```

####DataTypes
The common 

```
:binary 		=> Binary data
:boolean 		=> True/False 1/0
:date			=> 2015-06-03
:datetime		=> 2015-06-03 11:20:12
:decimal		=> 12.2340
:float			=> 12.333 (in binary format, something like scientific notation)
:integer		=> 1223233 -123424243
:references		=> Polymorphic association.
:string			=> "Lorem ipsum dolor" (255)
:text			=> "Long string"
```



#####Take advantage of the migration_name:
>If you name your migration in UpperCamelCase like: ```AddSomethingToPluralizedTableName``` or ```RemoveSomethingToPluralizedTableName``` and add the desired columns like ```field_name:data_type another_field:data_type``` add_column and remove_column statements will be added automatically.

#####Example:

```
rails g migration AddUserIdToProjects user_id:integer
```

return:

```
class AddUserIdToProjects < ActiveRecord::Migration
  def change
    add_column :projects, :user_id, :integer
  end
end
```

If you add ```:index``` after the ```field_name:data_type``` like ```field_name:data_type:index``` it will add the table index with this defined column.

####The model generator

```rails g model Task description:text```

#####Join tables
```
create_join_table :products, :categories
```

[Source: EdgeGuides](http://edgeguides.rubyonrails.org/active_record_migrations.html)


##ActiveRecord
Is a part of the Model of MVC, a layer of the system responsible ot the representation of business logic.

It handles the representation of stored information in the database.

It's an ORM (Object Relational Mapping system) a technique that connects the rich objects of an application to tables in a relational database.

###Conventions

- Database Table - Plural with underscores separating words (e.g., book_clubs).
- Model Class - Singular with the first letter of each word capitalized (e.g., BookClub).

| Model / Class	| Table / Schema |
|--------------|-----------------|
| User		| users |
| Article	| articles |
| LineItem	| line_items |
| Mouse		| mice |
| Person	| people |

- __Foreign keys__ - These fields should be named following the pattern singularized_table_name_id (e.g., item_id, order_id). These are the fields that Active Record will look for when you create associations between your models.
- __Primary keys__ - By default, Active Record will use an integer column named id as the table's primary key. When using Active Record Migrations to create your tables, this column will be automatically created.

- __created_at__ - Automatically gets set to the current date and time when the record is first created.
- __updated_at__ - Automatically gets set to the current date and time whenever the record is updated.

By default migration add the id[autoincrement] column as an index.

#### Override conventions
self.primary_key = "product_id"
self.table_name = "PRODUCT"

===

## Validations

You can add validations in your frontend, but adding model based validations a great plus


```
class Product < ActiveRecord::Base
  validates :name, presence: true
end
 
Product.create(name: "Fries").valid? # => true
Product.create(name: nil).valid? # => false

p = Product.new(name: nil)
product.errors.messages if p.invalid?
```
E.g.

```
validates :email_confirmation, presence: true
validates :name, exclusion: { in: %w(facebook twitter google), message: "%{value} is reserved." }
validates :size, inclusion: { in: %w(small medium large), message: "%{value} is not a valid size" }
```

####Length
```
class Person < ActiveRecord::Base
  validates :name, length: { minimum: 2 }
  validates :bio, length: { maximum: 500 }
  validates :password, length: { in: 6..20 }
  validates :registration_number, length: { is: 6 }
end
```

####Numericality
```
class Player < ActiveRecord::Base
  validates :points, numericality: true
  validates :games_played, numericality: { only_integer: true }
end
```

####Uniqueness
```
class Post < ActiveRecord::Base
  validates :slug, uniqueness: true
  # validates :slug, uniqueness: { scope: :year, message: "should happen once per year" }  
end
```

### Custom Validators

```
class CandidateValidator < ActiveModel::Validator
  def validate(record)
    if record.vote_for == "Ivonne Álvarez"
      record.errors[:base] << "OH noes! This person is evil"
    end
  end
end
 
class Person < ActiveRecord::Base
  validates_with CandidateValidator
end
```

####Validate in special cases: 
```validates :email, uniqueness: true, on: :create```

```
class Order < ActiveRecord::Base
  validates :card_number, presence: true, if: :paid_with_card?
 
  def paid_with_card?
    payment_type == "card"
  end  
end

class Person < ActiveRecord::Base
  validates :surname, presence: true, if: "name.nil?"
end

```
[Source ActiveRecordValidations](http://guides.rubyonrails.org/active_record_validations.html)

##Associations

### ```belongs_to``` & ```has_many```
one-to-one connection with another model
```
posts:
  id:integer
  title:string
  content:text
  author_id:integer
  #...

authors:
  id:integer
  name:string
  #...

class Post < ActiveRecord::Base
  belongs_to :author
end


class Author < ActiveRecord::Base
  has_many :posts
end
```

### ```has_one```

```
offices:
  id:integer
  name:string
  employee_id:integer
  #...

employees:
  id:integer
  name:string
  #...

class Employee < ActiveRecord::Base
  has_one :office
end

class Office < ActiveRecord::Base
  belongs_to :employee
end
```


### ```has_many :through```

```
posts:
  id:integer
  title:string
  content:text
  author_id:integer
  #...

authors:
  id:integer
  name:string
  #...

blog_photos:
  id:integer
  blog_id:integer
  photo_url:string

class Post < ActiveRecord::Base
  belongs_to :author
  has_many :photos
end


class Author < ActiveRecord::Base
  has_many :posts
  has_many :photos, through: :posts
end
```



### ```has_and_belongs_to_many```

```
users:
  id:integer
  name:string
  email:string

skills:
  id:integer
  name:string

```

We need an especial migration like: 
- both tablenames in plural
- tablename order alphabetically (skills_users because S is before U)

fortunelly in rails 4:
```rails generate migration CreateJoinTableSkillUser skill user```

```
create_table :skills_users, id: false do |t|
  t.belongs_to :skill, index: true
  t.belongs_to :user, index: true
end
```

```
skills_users:
  skill_id:integer
  user_id:integer
```


```
class User < ActiveRecord::Base
  has_and_belongs_to_many :skills
end


class Skill < ActiveRecord::Base
  has_and_belongs_to_many :users
end

in console:
> u = User.first
> u.skills.create(name: "kill")
> u.skills
> Skill.create(name: "dance")
> Skill.last.users
> Skill.create(name: "Drink Alcohol in high levels").users.create(name: "José José")
> u = User.create(name: "Ivonne Álvarez")
> u.skills.create(name: "Corruption")
```

### And the most epic of associations

# Polymorphic associations

One model associated to almost any model …including itself (mind blowing, kay, not!)

```
comments:
  id:integer
  content:text
  commentable_id:integer
  commentable_type:string

class Comment < ActiveRecord::Base
  belongs_to :commentable, polymorphic: true
end
 
class Post < ActiveRecord::Base
  has_many :comments, as: :commentable
end
 
class Video < ActiveRecord::Base
  has_many :comments, as: :commentable
end
```

[Source ActiveRecordAssociations](http://guides.rubyonrails.org/association_basics.html)

### ActiveRecord Query Interface

#### Model.query().another_query().another_and_another (chained methods)

### .find(«id»)

```
client = Client.find(@id)
```

### .create({})

```
client = Client.create({name: "Lorem", email: "Ipsum"})
```

### .build({})

```
client = Client.create({name: "Lorem", email: "Ipsum"})
```

