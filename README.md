===
#Ruby on Rails

###Topics:
- Intro:
  - What the hell is this?
  - Philosophy (DRY, COC).
  - MVC Architecture pattern.
  - RESTful Architecture.
- Getting Started with Rails
  - Basic commands.
  - The structure of a Rails app.
  - Planing.
  - Database modeling.
  - Hands on

===
####What the hell is this?
- An open source web application framework written in Ruby.
- An MVC framework (model–view–controller).
- Facilitates the use of web standards (XML/JSON) for data transfer.
- Created by David Heinemeier Hansson (DHH) in July 2004.
- Code easy to understand.

#####Philosophy:
- DRY (Don’t Repeat Yourself), aim you to reduce repetition of information and code in all kinds.
- COC (Convention Over Configuration), developer only needs to specify unconventional aspects of the application, if you respect the convention your code will remain clean, easy to read and work like a charm ;).

===
###MVC
Model-View-Controller, is a software architectural pattern that divides an application into three interconnected parts. (Don’t blame me, blame wikipedia)

  MVC:
    Model: It handles data and business logic.
  View: Presents data to the user in any supported format.
  Controller: Receive user requests as HTTP Verbs and call the appropriate resource.

===
###RESTful Architecture? dude, kind of boring stuff!
Representational State Transfer, A doctoral thesis by [Roy Fielding](http://es.wikipedia.org/wiki/Roy_Fielding)

An important concept in REST is the existence of resources (data elements), which can be accessed using a global identifier (Uniform Resource Identifier). To manipulate these resources

===

###The structure of a rails app.

This command creates a new rails application, a specific folder with all
the necessary files & folders.

| File/Folder     | Description |
|------------------|-------------|
| app/            | Contains all your application logic, such as Controllers, Assets, Mailers, Helpers, etc. |
| bin/            | Contains all the necessary scripts to run you application |
| config/         | All your application configuration lives here! for example the router, database configuration, etc. |
| db/             | contains your databse schema as well as your database migrations |
| Gemfile         | in this file you can specify all your application gem dependencies |
| lib/            | you can place here your files that extend a functionality in your app |
| log/            | here you have your application logs. |
| public/         | The only folder seen by the world as-is. Contains static files and compiled assets. |
| test/ or spec/  | Unit tests, fixtures, and other test apparatus. |
| tmp/        |  Temporary files (like cache, pid, and session files). |
| vendor/     | A place for all third-party code. In a typical Rails application this includes vendored gems. |

===

###Rails Basic Commands

| Command | Description |
|-----------|-------|-------------|
| `$ rails new «name»` | Create a new Ruby on Rails application with the given name here. This will give you the basic structure to immediately get started. After this command has successfully run your application is in a folder with the same name you gave the application. You have to cd into that folder. |
| `$ rails server` | You have to start the server in order for your application to respond to your requests. Starting the server might take some time. When it is done, you can access your application under localhost:3000 in the browser of your choice. In order to stop the server, go to the console where it is running and press Ctrl + C |
| `$ rake db:migrate` | When you add a new migration, for example by creating a new scaffold, the migration has to be applied to your database. The command is used to update your database. |
| `bundle install` | If you just added a new gem to your Gemfile you should run bundle install to install it. Don't forget to restart your server afterwards! |
| `$ rake routes` | Shows complete list of available routes in your application. |

```
# Checking rails version
  $ rails -v
# Installing rails
  $ ruby -v
  $ gem install rails
# Starting a new application:
  $ rails new «app_name»
```

===

###Controllers & actions …and the router

_«A controller is a Ruby class which inherits from ApplicationController and has methods just like any other class. When your application receives a request, the routing will determine which controller and action to run, then Rails creates an instance of that controller and runs the method with the same name as the action.»_

>__Convention:__ The naming convention of controllers in Rails favors pluralization of the last word in the controller's name, although it is not strictly required (e.g. ApplicationController). For example, ClientsController is preferable to ClientController, SiteAdminsController is preferable to SiteAdminController or SitesAdminsController, and so on.

```
class ApplicationController < ActionController::Base
  #…
end
```


```
class WelcomeController < ApplicationController
  #…
end
```

```
class WhateverController < ApplicationController
  #…
end
```

####Actions
Actions are methods in your controller class

```
class WelcomeController < ApplicationController
  def index
    #...
  end
  def new
    #...
  end
end
```

>every time a user makes a request, the rails router try to match this and serve the proper controller and action, so, to make this work, we need to add the proper route in routes.rb

```
# routes.rb
Rails.application.routes.draw do
  #...
  get "/", to: "welcome#index"

  #or:
  root to: 'welcome#index'

end
```

####Rendering
```
class WelcomeController < ApplicationController
  def index
    render text: "Sup wurld? kthxbye!"
  end
  def new
    #...
  end
end
```

