# Exercises

### 1) Playing with a rails app

Here’s some stuff to try on your own:

1. Create a new rails app, use whatever database manager you feel comfortable with2. Inside this new app create a controller called `StaticPages` with four actions: `home`, `about_me`, `surprise` & `help`

3. Add links for `about_me`, `help`, `surprise` pages inside `home` page
4. A call to the following Ruby method returns a list of all the files in the current directory:
```ruby@files = Dir.glob('*')
```Use it to set an instance variable inside `about_me` action and then write the corresponding template that displays the filenames in a list on the browser. You might want to use a `<ul>` for the list.

5. Write a small program inside `surprise` action that prints a two triangles build of "*", based on dynamic parameter. So if you send "6" the following triangles should be print in the view:

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

### 2) Difference Of Squares

1. In the same application generate another controller called `Squares`

2. Create a new action called `difference` inside the `Squares` controller, this action should receive a number as parameter


3. With the parameter received in 2), find the difference between the *sum of the squares* and the *square of the sums* of the first N natural numbers 

Example, if the action `difference` receives 10 as a parameter: 

The square of the sum of the first 10 natural numbers is,

    (1 + 2 + ... + 10)**2 = 55**2 = 3025

The sum of the squares of the first ten natural numbers is,

    1**2 + 2**2 + ... + 10**2 = 385

Hence the difference between the square of the sum of the first 10 natural numbers and the sum of the squares is 2640:

    3025 - 385 = 2640
 