# Lab-02: A simple cmd interface

In this lab we will take the rudimentary shopping list from lab-01 and expand it with more capabilities.

We will introduce functionality from the python `cmd` module to create a simple commandline interface.
This will allow our users (we care about our users) to access the full functionality of the shopping list without requiring them to write any python code.

## Setup

Create a file `/path/to/IMAT1912-labs/lab_02/lab_02.py`.

>On windows, this would be `C:\path\to\IMAT1912-labs\lab_02\lab_02.py` or similar.


>Remember, this is the folder structure you should be working in 
>```
>IMAT1912-labs
>├── lab_01
>│   ├── lab_01.py
>│   ├── experiment1.py
>│   └── experiment2.py
>└── lab_02
>    ├── lab_02.py
>    └── experiment3.py
>```
> again, create your own experiments in additional files within the `lab_02` folder.

We will start with an empty list stored in the `shopping` variable and a set of four functions, each of which handles a particular task.

Essentially, we want to have a complete set of operations as follows:

- add a new item to the list
- remove an item from the list
- edit an item in the list
- print the whole list

We did all this last week so here's an initial version of the code. Copy this into your file.

```python
shopping = []

def create(item):
    """Add a new item to the end of the list"""
    shopping.append(item)

def print_list(a_list):
    """print out the entire list"""
    lengths = [len(i) for i in a_list]
    n = max([20, *lengths])
    print()
    print('SHOPPING LIST'.center(n + 2, '='))
    if a_list:
        for i, item in enumerate(a_list):
            print(f'|{item.center(n)}| ({i})')
    else:
        print("EMPTY".center(n))
    print('=' * (n + 2))

def update(index, new_value):
    """replace the item at the given index"""
    shopping[index] = new_value

def delete(index):
    """delete the item at the given index"""
    del shopping[index]
```

Notice that the first content in all the functions is just a string containing a description of what the function does.
This is called a [docstring](https://www.python.org/dev/peps/pep-0257/) and acts as documentation for the function.
Using """triple quotes""" like this is [the usual convention](https://www.python.org/dev/peps/pep-0257/#one-line-docstrings) because it allows for docstrings to be extended over multiple lines as necessary.

>function docstrings can be accessed by inspecting the special `__doc__` property of the function itself.
>```python
>print(create.__doc__) # 'Add a new item to the end of the list'
>```
>docstrings are also common in classes and modules. You can peek ahead at the `cmd` module by running this code:
>```python
>import cmd
>print(cmd.__doc__)
>```
>docstrings are sometimes very long and detailed and can often replace the need for comments.

## Analysis

Now, let's go through the functions one by one and remind ourselves how the shopping list works.

### The `'create'` function

```python
def create(item):
    """Add a new item to the end of the list"""
    shopping.append(item)
```

We have simply extracted this functionality from the infinite loop in `lab_01.py` into a function so we can call it whenever we need it.
Our function appends a given item to the `shopping` list.
The calling code must pass in a string for everything to work correctly.

Try adding a few calls to the new `create()` function at the end of your file.

```python
create("apples")
create("bananas")
create("cherries")
print(shopping) # ['apples', 'bananas', 'cherries']
```

Seems pretty simple so far.

### The `'print_list'` function

We ended lab-01 with some challenges.
Here's my, slightly adjusted implementation for the `print_list()` function.

>An explanation is provided in the lab workthrough video and we will go over it here again.
If you are happy with what we did, you can skip this part.

```python
def print_list(a_list):
    """print out the entire list"""
    lengths = [len(i) for i in a_list]
    n = max([20, *lengths])
    print()
    print('SHOPPING LIST'.center(n + 2, '='))
    if a_list:
        for i, item in enumerate(a_list):
            print(f'|{item.center(n)}| ({i})')
    else:
        print("EMPTY".center(n))
    print('=' * (n + 2))
```

There are a few changes we will discuss, but first, try using the new function by replacing the `print` statement:

```python
create("apples")
create("bananas")
create("cherries")
print_list()
```

The output should be something like this:

```
====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
======================
```

#### List comprehension

The first line in our `print_list()` function is new.

```python
lengths = [len(i) for i in shopping]
```
We can read this as:
>
> *return a list containing the length of the item for each item in `shopping`*

This construct is known as a [list comprehension](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions).
List comprehensions are shorthand for a `for` loop that constructs a new list based on transforming (and/or filtering) an iterable (such as a list).

In this case, we are passing each item in the list `shopping` into the built in [len()](https://docs.python.org/3/library/functions.html#len) function and compiling a list of the results.
The variable name `i` can be anything you choose, in this case the `i` refers to an *item* in the list.

The result of the list comprehension is a new list containing the length of each string.
We assign this new list to the variable `lengths`.

>Some examples:
>
>```python
>lengths = [len(i) for i in ['apples', 'bananas', 'cherries']]
>print(lengths) # [6, 7, 8]
>```
>
>or
>
>```python
>lengths = [len(i) for i in ['d', 'fruit beginning with "e"', 'f']]
>print(lengths) # [1, 24, 1]
>```

List comprehensions are an extremely powerful and flexible feature of python.

#### List destructuring

The second line in our `print_list()` function calls the [max()](https://docs.python.org/3/library/functions.html#max) built in function.
`max()` is very simple, when passed an iterable object it will return the largest element in the iterable.

In this case we pass it an unusual construct.

```python
width = max([20, *lengths])
```

The asterix is the unusual part.
When used like this, an asterix denotes `destructuring` of the list into its component elements.

>Without the asterix we would end up with a two-element list:
>```python
>print([20, [5, 8, 4]])  # [20, [5, 8, 4]]
>```
>Adding the asterix, `destructures` the list into its elements.
>```python
>print([20, *[5, 8, 4]]) # [20, 5, 8, 4]
>```
>One way to think of this is that you are removing the square brackets from the list. 

So `[20, *lengths]` is actually creating a new list which prepends the value `20` onto a copy of the `lengths` list.
We pass this into `max` to return the maximum of the list which is assigned to the `width` variable.

So the minimum value `width` can take will be 20, but if our longest item is longer than 20 characters, it will determine the value of `width`.

>Don't worry if you didn't follow that.
>Just be aware that those first two lines are a neater way to do the following:
>
>```python
>width = 20
>for item in shopping:
>    if len(item) > width:
>        width = len(item)
>```
>or
>```python
>width = 20
>for item in shopping:
>    width = max([len(item), width])
>```
>
>There are many ways to get the same result, but good practice leads to simpler, more readable python code.

The `width` variable is used in the remainder of the function to determine the width of every line, much the same as we used the literal `20` in the previous lab exercise.

#### Catching the empty list

The `for` loop is the core of the `print_list` function from last week.
It prints out each element in the list, formatted centrally within the width specified.

There have been some significant changes.

```python
if a_list:
    for i, item in enumerate(a_list):
        print(f'|{item.center(n)}| ({i})')
else:
    print("EMPTY".center(n))
```

We can see the `for` loop has been nested inside an [if/else](https://docs.python.org/3/tutorial/controlflow.html#if-statements) statement to capture the case when the list is empty.
Remember that an empty list evaluates to `False` when used in an `if` or `while` expression.

#### Using enumerate

There is also a major change to the `for` loop itself.

Rather than iterating over the list directly, our for loop now has two variables (`i` and `item`) and is iterating over the results of passing our list into the built in [enumerate](https://docs.python.org/3/library/functions.html#enumerate) function.

`enumerate` adds a counter that we can use to number our items.
It loops over the list and yields a pair of values containing the current count and each element in turn.

>`enumerate` is equivalent to this:
>```python
>def enumerate(sequence, start=0):
>    n = start
>    for elem in sequence:
>        yield n, elem
>        n += 1
>```
>
>We haven't mentioned `yield` yet, but the intent should be clear.
>Every item in the list is paired with an integer representing its index.
>
>functions that use `yield` are known as [generator functions](https://docs.python.org/3/howto/functional.html#generators).

finally, let's look at the print statement itself:

```python
print(f'|{item.center(n)}| ({i})')
```

Here we are using an [f-string](https://docs.python.org/3/tutorial/inputoutput.html#formatted-string-literals) (or `formatted string literal`) to create a string which is composed of the centered item with its padding and border plus an additional space and the index in brackets.

It should be clear to you how this all adds up to the output we see.

```
====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
======================
```

### The `'update'` function

To edit an item in our list, we only need to specify which item and provide a new value.


```python
def update(index, new_value):
    shopping[index] = new_value
```

We covered list indexing briefly last time.

```python
update(2, "cranberries")
```

### The `'delete'` function

Deleting is similar to updating, except we need only specify the index.

```python
def delete(index):
    del shopping[index]
```

When used with a list item, the [del](https://docs.python.org/3/tutorial/datastructures.html#the-del-statement) statement removes the element from the list.

>An alternative we might prefer is to use [list.pop](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists) to remove and return an item from the list.
>
>This would allow us to return the value we are deleting:
>```python
>def delete(index):
>    return shopping.pop([index])
>```
>This achieves the same result but gives us information about the value removed.
>```python
>print(f"{delete(2)} removed from shopping list")
>```
>This would also allow us to e.g. implement *undo* functionality by storing the removed item elsewhere.


## Stop and think

So, now we have the full list of four functions for the classic CRUD operations (Create, Read, Update and Destroy).
These functions control access to our list and make it easy to write code to create a list.

We can experiment a bit by writing a simple script which uses our shopping list code.
Create a new file `user.py` in the `lab_02` folder and enter the following code.

```python
from lab_02 import create, update, delete, print_list, shopping

create("python")
create("is")
create("complicated")
create("and")
create("difficult")
create("I don't understand")
create("I give up")

update(2, "powerful")
update(4, "intuitive")
update(5, "I get it, kinda")
delete(6)
create("I will...")
create("practice to build experience")
create("experiment to discover")
create("ask questions to clarify")
print_list(shopping)
```

In our new file, we are importing our shopping list code and creating an actual shopping list as if we were a user of the system.

Note that the implementation and naming of the various functions will determine the code needed to create a specific shopping list.
In other words, we are writing an application programmable interface (API) for our users.
We need to consider our users' experience when we define our API.

>In many cases **you** will be your only user.
This doesn't mean you shouldn't care about good API design.

We have separated the roles of API developer and API consumer into our two python modules. The `lab_02.py` module defines the API and the `user.py` module is where we put some example code that consumes the API.

>If you have any questions or comments about anything we have covered so far, now is a good time to articulate them.

## We can do better

Our `shopping` list API is implemented with four separate functions, three of which are directly accessing the module-level variable, `shopping`.
Our `print_list` function is different, it doesn't reference the `shopping` list at all.
This seems like an advantage.

We could pass in any list of strings to the function and it would dutifully print out the list as requested.
The other functions are tied directly into the specific list `shopping` declared at the top of the file.

This is a bit confusing.
Could we make all the functions general purpose in the same way?

A great way to define functions which are tied to particular data is to create a [class](https://docs.python.org/3/tutorial/classes.html#class-objects) to hold both the data and the functions.

We can start by creating a class with an `__init__` method.
The `__init__` method is called every time we create a new `instance` of the class.

```python
class ShoppingList:
    def __init__(self):
        self.items = []
```

In this case, we give each `instance` a unique list of items.
Instance methods, such as `__init__` have access to a special argument `self` which represents the object itself.

So we can adapt our methods as follows.

```python
class ShoppingList:
    def __init__(self):
        self.items = []

    def add(self, item):
        """Add a new item to the end of the list"""
        self.items.append(item)

    def print(self):
        """print out the entire list"""
        lengths = [len(i) for i in self.items]
        n = max([20, *lengths])
        print()
        print('SHOPPING LIST'.center(n + 2, '='))
        if self.items:
            for i, item in enumerate(self.items):
                print(f'|{item.center(n)}| ({i})')
        else:
            print("EMPTY".center(n))
        print('=' * (n + 2))

    def edit(self, index, new_value):
        """replace the item at the given index"""
        self.items[index] = new_value

    def delete(self, index):
        """delete the item at the given index"""
        del self.items[index]
```

Notice that all we needed to do was:
- indent the functions within the class body
-  add `self` as a new first argument to each function
- in the case of `print_list`, we replaced the `a_list` argument with `self`
- replace references to `shopping` (or `a_list`) with `self.items`

We also changed the names of the functions. 
>The first name we think of for a method is not always a good one.
Try using your code for a bit and if your class and function names don't make sense, change them.

Now, we can do a neat trick in our `user.py` file.

```python
from lab_02 import ShoppingList

fruit = ShoppingList()
veg = ShoppingList()

fruit.add('apples')
fruit.add('bananas')
fruit.add('cherries')

veg.add('artichokes')
veg.add('broccoli')
veg.add('celery')

fruit.print()
veg.print()

```

>output from the above
>```
>====SHOPPING LIST=====
>|       apples       | (0)
>|      bananas       | (1)
>|      cherries      | (2)
>======================
>
>====SHOPPING LIST=====
>|     artichokes     | (0)
>|      broccoli      | (1)
>|       celery       | (2)
>======================
>```

So we can now maintain as many separate lists as we need.

### A custom title

With a little more tweaking, we can personalise each list with a custom title.
Update the `__init__` function to accept a keyword argument `title` with a default value `'shopping list'`.

```python
def __init__(self, title='shopping list'):
    self.items = []
    self.title = title
```

Then update the print function to use our title.

```python
def print(self):
    """print out the entire list"""
    content = [self.title, *self.items] # a new line
    lengths = [len(i) for i in content] # notice this change
    n = max([20, *lengths])
    print()
    print(self.title.upper().center(n + 2, '=')) # and here
    if self.items:
        for i, item in enumerate(self.items):
            print(f'|{item.center(n)}| ({i})')
    else:
        print("EMPTY".center(n))
    print('=' * (n + 2))
```

Now we can update our code like so:

```python
fruit = ShoppingList(title='fruit')
veg = ShoppingList(title='veg')
```

which makes this output
```
========FRUIT=========
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
======================

=========VEG==========
|     artichokes     | (0)
|      broccoli      | (1)
|       celery       | (2)
======================
```

### Using `'__str__'`

Finally, we can change our `ShoppingList.print` method into a special [object.\_\_str__](https://docs.python.org/3/reference/datamodel.html#object.__str__) method to return a "*nicely printable string representation of [our] object*"

This is a broken down version, based on several helper functions.

>class attributes (including methods) named with a leading underscore are *by convention* not intended for use from outside the class and are only intended for use from within the class itself.
This convention helps us to break complex methods down into component parts.

```python
def _header(self, width):
    return self.title.upper().center(width + 2, '=')

def _items(self, width):
    if self.items:
        return [f'|{i.center(width)}| ({n})' for n, i in enumerate(self.items)]
    else:
        return [f'|{"EMPTY".center(width)}|']

def _lines(self, width):
    return [
        "", 
        self._header(width), 
        *self._items(width), 
        '=' * (width + 2)
    ]

def __str__(self):
    """print out the entire list"""
    content = [self.title, *self.items]
    lengths = [len(i) for i in content]
    w = max([20, *lengths])
    return '\n'.join(self._lines(w))
```
>In the above code, our `__str__` method calls the `_lines` method which calls the `_items` and `_header` methods.

By defining a `__str__` method we can convert our shopping list into a string and it can be printed in the normal way. 
Our *testing* code can now be updated as follows:

```python

shopping = ShoppingList()
fruit = ShoppingList(title='fruit')
veg = ShoppingList(title='veg')

fruit.add('apples')
fruit.add('bananas')
fruit.add('cherries')

veg.add('artichokes')
veg.add('broccoli')
veg.add('celery')

print(shopping)
print(fruit)
print(veg)
```

Our code is becoming more and more flexible and easy to use.


## A commandline interface

Our class seem to be quite usable, but the above script isn't very useful for building a shopping list dynamically.
We need a way to get user input, like we did last week.

>Wow! Remember this from last week? 
>That was awesome!
>```python
>shopping = []
>while True:
>    print_list(shopping)
>    item = input('Item (leave blank to exit): ')
>    if not item:
>        break
>    shopping.append(item)
>```
>Read this through and remember how it worked.

For the remainder of this session, we will use the [cmd](https://docs.python.org/3/library/cmd.html) module to build something a bit more sophisticated.

To get started, we need to inherit from the [cmd.Cmd](https://docs.python.org/3/library/cmd.html#cmd.Cmd) class.
This will give our class the core functionality of a command interpreter and allow us to focus on the detail of our application.

We can also upgrade our `__init__` method to set some attributes and call `super().__init__()`.

```python
class ShoppingList(cmd.Cmd): # notice the change here
    def __init__(self, title='shopping list'):
        self.items = []
        self.title = title
        self.intro = "\nMy awesome shopping list app"
        self.prompt = "\nshopping: "
        super().__init__()
```

>`super.__init__()` is how we call the `cmd.Cmd.__init__()` method which does some important initialisation. 

We have defined a class which extends the `cmd.Cmd` framework and specified two new attributes: 
- `intro` is the text that will be printed when the programme starts. 
- `prompt` is the text that will prompt the user for the next command.

Now we can call the `cmd.Cmd.cmdloop` method.

```python
shopping = ShoppingList()
shopping.cmdloop()
```

>Hey, we inherited this method from our parent class.
Nice.

This starts an infinite loop, similar to what we developed last week, but with more sophisticated features baked in.
Even without any further customisation, we already have a limited but working application.
>
>Run your code and you should see the intro message and command prompt printed.
>```
>My awesome shopping list app
>
>shopping: 
>```
>You can input a command, but the only commands it will respond to by default are `'help'` and `'?'` which both produce this output:
>```
>My awesome shopping list app
>shopping: help
>
>Documented commands (type help <topic>):
>========================================
>help
>```
>All other commands lead to an error message.
>```
>shopping: add apples
>*** Unknown syntax: add apples
>```
>
>We can also pass an argument to the `help` command, but it must be the name of one of the listed commands.
>
> Try typing `help help`
>```
>list: help help
>List available commands with "help" or detailed help with "help cmd".
>```
>Already, we can see its a lot more sophisticated than our simple loop.
>
>To exit the programme, press `Ctrl+C`.

The `cmd` module provides a framework for implementing very simple command interpreters.
Basically, it will create the infinite loop for us, requesting user input and passing it into functions we specify.

A core feature of the `cmd.Cmd` class is that when the user issues a command, it will be automatically routed to the custom methods we define on our class.
It also has a load of convenient and flexible functionality.

>`cmd` is implemented in pure python if you want to have a look at the [source code](https://github.com/python/cpython/blob/3.9/Lib/cmd.py).
Feel free to ask if there is something you want to understand in there.

### What's happening? I'm confused!

Let's walk through the core of what `cmd` does. 
The `cmd.Cmd.cmdloop` method is an elaboration on something a bit like this:
```python
def cmdloop(self):
    stop = None
    while not stop:
        line = input(self.prompt)
        chunks = line.split(maxsplit=1)
        command = chunks.pop(0)
        arg = chunks[0] if chunks else ''
        do_command = getattr(self, f'do_{command}')
        stop = do_command(arg)
```
>this is simplified for illustrations purposes. The real method is more complicated, though the core logic is similar.

The loop continually prompts the user for input and executes commands until the `stop` variable is set to `True`.

When a user enters a string into the prompt, the system breaks the string into two variables (`command` and `arg`), on the first space it finds.
If there is no space, then `arg` is set to an empty string.

>[str.split](https://docs.python.org/3/library/stdtypes.html#str.split) divides strings into chunks based on *whitespace* by default, and can be restricted to a maximum number of splits.
>```python
>chunks = line.split(maxsplit=1)
>```
>Which is very useful because we don't want to split the string on all spaces in this case.

For example, given this input:
```
shopping: add apples and pears
```

The *command* variable becomes `'add'` and the *arg* variable is set to `'apples and pears'`.
The string is split on the *first* space only, later spaces are ignored.

The system then looks for a method named `do_[command]` (i.e. `do_add`) and will call it, passing `arg` as the only argument.

>[getattr](https://docs.python.org/3/library/functions.html#getattr) is a useful built in function for accessing attributes (methods in this case) based on strings.
It allows us to construct the string `do_add` and see if we have an attribute of that name.
>```python
>do_command = getattr(self, f'do_{command}')
>stop = do_command(arg)
>```
>In our example case, the above code will do something equivalent to this.
>```python
>stop = self.do_add('apples and pears')
>```
>Which gives us the tools necessary to define our command line interface.

So we need to define special methods beginning `do_` which take a single string argument and optionally return `True` to end the loop.

>If you have any questions about this, just ask.

### A `'quit'` command

Let's start by implementing a way to exit the programme.

The user should type `'quit'` to exit.
We know that if a user enters the command `'quit'`, the `cmd.Cmd` system will look for a method named `do_quit` and will call it with an empty string as the single argument.
All we need to do is return `True`.
So we can add a method like this:

```python
def do_quit(self, arg):
    """Quit the shopping list programme"""
    return True
```

Now try running our programme and typing `quit` as a command, the programme should exit.

>As expected, if you enter `help quit`, you are presented with the `docstring` from our method.
>```
>shopping: help quit
>Quit the shopping list programme
>```
>This is very handy for our users.

### An `'add'` command

If a user enters the command `'add apples'`, the `cmd.Cmd` system will look for a method named `do_add` and will pass a string `'apples'` as the argument.
So we can change the name of our existing `add` method like this:

```python
def do_add(self, item):
    """Add a new item to the end of the list"""
    self.items.append(item)
```

This now works, but we have no way to see that it worked.
We want to show the updated list every time a new item is added.

Rather than implementing a `print` command, we can show the list every time we issue a command.
We do this by adding a `postcmd` method.

>The `cmd.Cmd.postcmd` method is called after every command is processed.
It receives two arguments, the value of `stop` and the `line` that was executed.
It must return a new value for `stop`.
We have the option to return `True` if we want to cancel the command execution.
In our case, we simply `print` the `self` argument (calling our existing `__str__` method) and return `stop` unchanged.

```python
def postcmd(self, stop, line):
    print(self)
    return stop
```

Try using the app.
Notice that the list is printed out after every command.

```
My awesome shopping list app

shopping: add apples

====SHOPPING LIST=====
|       apples       | (0)
======================

shopping: add bananas

====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
======================

shopping: add cherries

====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
======================

shopping: quit

====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
======================

```
>remember to type `quit` to exit

Also, notice that we have added a command to the documentation and that the `docstring` of our `do_add` method has been picked up by the help system.

```
My awesome shopping list app

shopping: help

Documented commands (type help <topic>):
========================================
add  help  quit


====SHOPPING LIST=====
|       EMPTY        |
======================

shopping: help add
Add a new item to the end of the list

====SHOPPING LIST=====
|       EMPTY        |
======================

shopping: 
```

If we want to avoid printing the entire list when executing the `'help'` command then we can modify our `postcmd` implementation.

```python
def postcmd(self, stop, line):
    if not line.startswith('help'):
        print(self)
    return stop
```

### A `'delete'` command


Now, update the `delete` method to `do_delete`.
Remember, the argument (that we have called `index`) will be a string, whatever the user enters after the command `delete`.
We need to convert this string into an integer.

```python
def do_delete(self, index):
    """delete the item at the given index"""
    index = int(index) # convert a string to an integer
    del self.items[index]
```

Now, if we enter a suitable integer, we can delete an element from the list.

```
shopping: add mistake

====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
|      mistake       | (3)
======================

shopping: delete 3

====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
======================

shopping: 
```

Our most complicated method is `do_edit`.
Update your `edit` method as follows:

```python
def do_edit(self, args):
    """replace the item at the given index"""
    index, new_value = args.split(maxsplit=1)
    index = int(index)
    self.items[index] = new_value
```

Note that we have changed the arguments from `index` and `new_value` to `args` because the method will be called with whatever string the user enters after the `edit` command.

So we have added some code to split our string into two pieces and to convert the first one (`index`) into an integer.
This all works perfectly (as long as the user follows the rules).

```
shopping: add chorries

====SHOPPING LIST=====
|       apple        | (0)
|       banana       | (1)
|      chorries      | (2)
======================

shopping: edit 2 I mean cherries

====SHOPPING LIST=====
|       apple        | (0)
|       banana       | (1)
|  I mean cherries   | (2)
======================

shopping: 

```

Our final file looks like this:

```python
import cmd

class ShoppingList(cmd.Cmd):
    def __init__(self, title="shopping list"):
        self.items = []
        self.title = title
        self.intro = "\nMy awesome shopping list app"
        self.prompt = "\nshopping: "
        super().__init__()

    def postcmd(self, stop, line):
        if not line.startswith('help'):
            print(self)
        return stop

    def do_add(self, item):
        """Add a new item to the end of the list"""
        self.items.append(item)

    def do_quit(self, arg):
        """Quit the shopping list programme"""
        exit()

    def _header(self, width):
        return self.title.upper().center(width + 2, '=')

    def _items(self, width):
        if self.items:
            return [f'|{i.center(width)}| ({n})' for n, i in enumerate(self.items)]
        else:
            return [f'|{"EMPTY".center(width)}|']

    def _lines(self, width):
        return [
            "", 
            self._header(width), 
            *self._items(width), 
            '=' * (width + 2)
        ]

    def __str__(self):
        """print out the entire list"""
        content = [self.title, *self.items]
        lengths = [len(i) for i in content]
        w = max([20, *lengths])
        return '\n'.join(self._lines(w))
        
    def do_edit(self, args):
        """replace the item at the given index"""
        index, new_value = args.split(maxsplit=1)
        index = int(index)
        self.items[index] = new_value

    def do_delete(self, index):
        """delete the item at the given index"""
        index = int(index)
        del self.items[index]

shopping = ShoppingList()
shopping.cmdloop()
```

>We don't need to understand the [implementation details](https://github.com/python/cpython/blob/3.9/Lib/cmd.py) within the `cmd` module, but make sure you understand what is happening in our code before moving on.

Notice how we have specified a single argument (in addition to `self`) for each of the special `do_[command]` methods.
This argument is *always* a string and we process it to parse out the information we expect.

In the case of `do_add` the argument is the new item which is added (a string) and so does not need any processing.

The `do_delete` method expects an integer index only and so simply passes the string argument through [int()](https://docs.python.org/3/library/functions.html#int) to extract the information it needs.

The `do_edit` method is the most complex.
We need to split the provided string into two parts, the first argument should be an integer and the remainder is the string to set as the item value. 
Once split (using [str.split()](https://docs.python.org/3/library/stdtypes.html#str.split)), the index is cast to an integer using [int()](https://docs.python.org/3/library/functions.html#int).

Try running the programme and calling the allowed commands.

- `add apples`
- `add bananas`
- `add cabbages`
- `edit 2 cherries`
- `add python is difficult`
- `delete 3`
- `add python is powerful`

We now have the basis for a complete shopping list project.
Only a few steps remain before we can release this to our users.

## Conclusion

We have covered a lot of territory in this lab. 
In particular, we have introduced [classes](https://docs.python.org/3/tutorial/classes.html) which allow us to develop our own object types with our own methods.
We also introduced a number of special so-called [dunder methods](https://docs.python.org/3/reference/datamodel.html#special-method-names) ([\_\_init__](https://docs.python.org/3/reference/datamodel.html#object.__init__) and [\_\_str__](https://docs.python.org/3/reference/datamodel.html#object.__str__)) which have a special meaning and allow access to special syntax.

We also demonstrated [inheritance](https://docs.python.org/3/tutorial/classes.html#inheritance) which allowed us to easily create a command line interpreter inheriting core functionality from the [cmd](https://docs.python.org/3/library/cmd.html) module.

Making use of the python standard library is important. 
The standard library code is battle tested and written by people who know how to write excellent python code.

# Challenges

Go through these and see if you can implement as many as possible before next week.

## 1. Implement `'load'` and `'save'` commands

Obviously, we really need our list to persist beween sessions.

The `load` command should open a file and read in items from the file. 
The `save` command should open a file and write the list into it.

>You will need to research [reading and writing files](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files).
>
>The (newish) `pathlib` library is extremely convenient for specifying paths and opening files.


## 2. Load and save your list automatically

This should be default behaviour. We want to automatically load data and print the list before the command loop starts.
We also want to save data when the loop exits.

>Check out the `cmd` [documentation](https://docs.python.org/3/library/cmd.html) and [implementation](https://github.com/python/cpython/blob/3.9/Lib/cmd.py) for clues.

## 3. Add a `'clear'` command
Now we have a persistent list, its useful to have a simple command to clear the whole list.

