### Exercises

Here’s some stuff to try on your own:

- Create a new rails app, use whatever database manager you feel comfortable with- Inside this new app create a controller called `StaticPages` with five actions: `home`, `about_me`, `surprise` & `help`

- Add links for `about_me`, `help`, `surprise` pages inside `home` page
- A call to the following Ruby method returns a list of all the files in the current directory:
```ruby@files = Dir.glob('*')
```Use it to set an instance variable inside `about_me` action and then write the corresponding template that displays the filenames in a list on the browser. You might want to use a `<ul>` for the list.

- Write a small program inside `surprise` action that prints a two triangles build of "*", based on dynamic parameter. So if you send "6" the following triangles should be print in the view:

```ruby
# Triangle 1
* 
** 
*** 
**** 
***** 
****** 

# Triangle 2
***********
 *********
  *******
   *****
    ***
     *
```

And if you send "4"

```ruby
# Triangle 1
* 
** 
*** 
**** 

# Triangle 2
*******
 *****
  ***
   *
```
- **Bonus points**: Make `home` appear at first, everytime you go to `/` inside your application (e.g when you run in your browser `localhost:3000`, the first page that appear should be `home`). You might want to check rails routing [guide](http://guides.rubyonrails.org/routing.html)