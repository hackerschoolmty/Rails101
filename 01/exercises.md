### Exercises

Here’s some stuff to try on your own:

- Create a new rails app, use whatever database manager you feel comfortable with- Inside this new app create a controller called `StaticPages` with three actions: `home`, `about_me` & `help`

- Add links for `about_me` & `help` pages inside `home` page
- A call to the following Ruby method returns a list of all the files in the current directory:
```ruby@files = Dir.glob('*')
```Use it to set an instance variable inside `about_me` action and then write the corresponding template that displays the filenames in a list on the browser. You might want to use a `<ul>` for the list.

- In the `help` page write each rails command that you learned today

- **Bonus points**: Make `home` appear at first, everytime you go to `/` inside your application (e.g when you run in your browser `localhost:3000`, the first page that appear should be `home`). You might want to check rails routing [guide](http://guides.rubyonrails.org/routing.html)