## Hackershool directory guide


**Steps**

1. Fork hackerschool_directory repository from [here]()
2. Run `bundle install` to install any dependency
3. Create hacker &  programming language model as stated in [Model attributes](#model-attributes)
	* Models can be created using rails g model generator o scaffold generator
4. Complete user stories described in [here](#user-stories)
5. Make a PR to master once you have finished.

## Model attributes 

```
- hacker
	- name
	- email
	- github_account
	
- project
	- title
	- hacker_id

- programming_language
	- name
	
```

###  Validations

- A hacker must have a name, email and github account associated
- Github accounts must be unique
- A hacker can have many projects related 
- A hacker can have several programming language related

### User stories

* As a user the first page that I want to see is the main directory ("/hackers")
* As a user I have access to all CRUD operations for hackers, projects and programming languages
* As a user I can associate skils & projects to hackers
