# Lab-01: Lists

In this lab, we will build a partially functional *shopping list* application.
The programme will allow the user to add items to a list and will output the list to the terminal each time a new item is added.

>Ostensibly, this lab is about lists. 
However, we will be using many different features of python together in order to build a simple programme.

>Going through this process as a python beginner may provide you with more questions than answers.
This is a good thing.

>Make sure you ask whenever something surprises you or if you want to clarify a generalisation.

> Also, elaborate on the provided examples and play around with python to check your understanding and see what you can do.

## Preparing a folder structure

We are going to be writing python scripts into files and executing them using the `python3` commandline programme.

You are encouraged to generate multiple files as you experiment each week.
Its important to organise your files carefully.
A folder structure like this is recommended.

```
IMAT1912-labs
├── lab_01
│   ├── lab_01.py
│   ├── a_while_loop.py
│   └── an_experiment.py
└── lab_02
    ├── lab_02.py
    └── another_experiment.py
```
In the above, only the `lab_01.py` and `lab_02.py` files are provided.
The assumption is that you will keep snippets and name them yourself according to your preferences.

In python a file is called a `module`.
From the official [style guide for python code](https://www.python.org/dev/peps/pep-0008/): 
>*"Modules should have short, all-lowercase names. Underscores can be used in the module name if it improves readability."* 

Create an `IMAT1912-labs` folder if you don't already have one.

Create a `lab_01` folder within the `IMAT1912-labs` folder to keep today's files organised.

Create a file `lab_01.py` for the code we will generate below.

As you work through the exercise, try to keep the `lab_01.py` folder clean, only including the code in the exercise.
Feel free to experiment and produce more files, with snippets of code that you want to remember and write comments in those files to help you when you review the code later.

```python
# python comments are written like this
```

## Set up

OK, let's get started by writing some code into our file.

```python
print('hello world!')
```

> you can use any text editor for this [atom](https://atom.io), [vscode](https://code.visualstudio.com/), [sublime text](https://www.sublimetext.com/) etc..
> the lab machines should have many options already installed.

We can execute the code in the windows cmd commandline or powershell or in a bash terminal on linux/mac.
First we need to change to the appropriate directory.

```bash
cd /path/to/IMAT1912-labs/lab_01
```

> your path will be different, depending on swhere you have created the `IMAT1912-labs` folder.

We can execute the programme like this:

```bash
python3 lab_01.py
```

This should output your friendly greeting.
If not, check the error message and consider:

- Is python3 installed properly?
- Does the specified file exist in the current directory?
- Does the file contain valid python code?

If you can't work it out, ask your tutor.

Assuming you have a working development environment, let's start with our simple programme.

## A list

The first step is to create the data structure for our shopping list.
Conveniently, python provides a datastructure known as a [list](https://docs.python.org/3/tutorial/introduction.html#lists).
Lists are a compound data-type used to group together other values.
Lists contain items (which can be anything) in a given order.

Starting with an empty shopping list, we use the square bracket notation.

```python
shopping = []
```

>To create an empty list, we could alternatively have called the built-in [list()](https://docs.python.org/3/library/functions.html#func-list) function and passed nothing to it.
>```python
>shopping = list()
>```
>[list()](https://docs.python.org/3/library/functions.html#func-list) converts any [iterable](https://docs.python.org/3/glossary.html#term-iterable) object to a list.
>Both lists and strings are iterable.
>Passing nothing into the function makes a list containing nothing.


## Viewing the list

As we saw above, we can use the builtin [print()](https://docs.python.org/3/library/functions.html#print) method to output any values to the console.

```python
print(shopping)  # []
```

>print() can also output to files, or any other object pretending to be a file.
But its usually used for outputing to the terminal. 

## Add an item to the list

An empty list is only so much use. 
Let's get some apples.

```python
shopping = []
shopping.append('apples')
```

Here we have added the string `'apples'` to the list. 
If we print the list now, we can see the fruits of our labour.

```python
print(shopping)  # ['apples']
```

>As alluded to above, strings are sequences of characters and are also iterable.
So this is perfectly normal in python.
>```python
>ch = list("apples")
>print(ch) # ['a', 'p', 'p', 'l', 'e', 's']
>```

Alternatively, we could instantiate the list with items already in it using comma separated values.

```python
shopping = ['apples', 'bananas']
print(shopping)  # ['apples', 'bananas']
```

>In our shopping list, we want our values to be strings, but lists can contain any type, even expressions.
Mixing types within a list is not a problem.
>```python
>my_list = [1, [2, 3], 'apples', 4.1, 10/3, None]
>```
>Python will happily group anything into a list.

## Retreiving items from the list

Starting with a few items in our list. 

```python
shopping = [
    "apples",
    "bananas",
    "clementines"
]
```

>Using consistent style is considered very important in the python community.
The [style guide for python code](https://www.python.org/dev/peps/pep-0008/) includes naming conventions and formatting guides for all python constructs, including multiline formatting for lists.

We can access the item at a given position using square bracket notation.

```python
print(shopping[0]) # 'apples'
```

We can also overwrite an item in the same way.

```python
shopping[3] = "cranberries"
print(shopping) # ['apples', 'bananas', 'cranberries']
```

## Getting user input

Let's add a feature.
We want to get a string from the user and add it to the list.

First, we get the string using the built in [input()](https://docs.python.org/3/library/functions.html#input) function.

The `input()` function takes one argument, a string which is used as a prompt for the user.

Try this:

```python
result = input("type something here -> ")
print(result) # 'this is what I typed'
```

So we can add an item to our list by putting the code together into a small programme.

```python
shopping = []
item = input('Add an item: ')
shopping.append(item)
print(shopping)
```

If there is anything you don't understand above, ask NOW.
We are about to get a bit more complicated.

## An infinite loop

We are aiming to create a programme that can add more than one item to our list.
This will require calling the `input()` function multiple times to get each item from the user.

We could do something like this:

```python
shopping = []
print(shopping)
item = input('New item: ')
shopping.append(item)
print(shopping)
item = input('New item: ')
shopping.append(item)
print(shopping)
item = input('New item: ')
shopping.append(item)
print(shopping)
item = input('New item: ')
shopping.append(item)
print(shopping)
```
> notice we are now printing the list each time, so the user can see what they have already added.

The problem here is that we always ask the user for four items and we don't really know how many items our users will want to add.
If they want three or five items, they get a bad experience.

A typical solution to a problem like this is to create an infinite loop and explicitly break out of the loop when the user tells us they are finished.

First, we will create an infinite loop using a [while statement](https://docs.python.org/3/reference/compound_stmts.html#while).

```python
shopping = []
while True:
    print(shopping)
    item = input('Item: ')
    shopping.append(item)
```

Running this code creates a problem.
The loop is infinite.

> Use Ctrl + C to raise a `KeyboardInterrupt` error and break out of the programme.

So we will add a simple rule to our programme.
Using an [if](https://docs.python.org/3/tutorial/controlflow.html#if-statements) statement we can test whether the provided string is empty.
If the user enters a blank string, then we will [break](https://docs.python.org/3/tutorial/controlflow.html#break-and-continue-statements-and-else-clauses-on-loops) out of the loop.

```python
shopping = []
while True:
    print(shopping)
    item = input('Item (leave blank to exit): ')
    if item == '':
        break
    shopping.append(item)
```

Now in python, most things can be tested for its [truth value](https://docs.python.org/3/library/stdtypes.html#truth-value-testing).
Things like empty lists and empty strings are considered false.
This allows a small improvement in readibility.

```python
shopping = []
while True:
    print(shopping)
    item = input('Item (leave blank to exit): ')
    if not item:
        break
    shopping.append(item)
```

>Python [boolean operations](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not) include `and`, `or` and `not`.

## Formatting the output

The printed list is functional but not very nice.
Let's end the first lab by introducing [functions](https://docs.python.org/3/tutorial/controlflow.html#defining-functions).
We need a function that will print out the list items in a nice format.

Update your code to call our function instead of `print()`.

```python
shopping = []
while True:
    read(shopping)
    item = input('Item (leave blank to exit): ')
    if not item:
        break
    shopping.append(item)
```

Now we can define our `read` function using the `def` keyword.
The function takes one argument, the list to be printed.

```python
def read(shopping_list):
    print('\n')
    print('=' * 20)
    print('SHOPPING LIST'.center(20))
    print('=' * 20)
    for item in shopping_list:
        print(item.center(20))
    print('=' * 20)
    print('\n')
```

On the first and last lines of the function body, we print a newline character to create some space around the list.

```python
print('\n')
```

We also see this line three times.

```python
print('=' * 20)
```

We use it at the top and bottom of the list and to separate the list content from the list header.

The code prints a series of twenty '=' characters.
In python strings can be multiplied by integers to repeat the string multiple times.
Its strange but sometimes useful.

The header contains the string 'SHOPPING LIST' between two lines of twenty '=' characters.

```python
print('=' * 20)
print('SHOPPING LIST'.center(20))
print('=' * 20)
```

We call the string method [str.center](https://docs.python.org/3/library/stdtypes.html#str.center) to expand the string 'SHOPPING' to 20 characters by padding either side with spaces.

Then we have a [for](https://docs.python.org/3/tutorial/controlflow.html#for-statements) loop. 

```python
for item in shopping_list:
    print(item.center(20))
print('=' * 20)
```

Each value in our list is assigned in turn to the variable `item`.
Again, we call the `str.center` method to pad our string with spaces before we print it.

Finally, we print another line of '=' characters to end the list.

Try running the programme with our new `read` function.

## Conclusion

In this first lab, we have introduced the following concepts:

- the built in `print()` function
- the built in `list()` function
- the built in `input()` function
- lists (creating, appending, indexing)
- truthyness of objects (e.g. empty strings)
- string manipulation with `*` and `str.center`
- conditionals (`if`/`else`)
- `for` loops and `iterators`
- tuple decomposition
- enumerate
- while loops (infinite with break)
- function definitions and arguments

## Challenges

Upgrade the `read` function to meet the following requirements.

### 1. Handle an empty list

Print "EMPTY" inside the list when its empty (not otherwise).

```
====================
   SHOPPING LIST    
====================
       EMPTY        
====================
```

>remember, empty lists are considered False.

### 2. Dynamic formatting

Upgrade the `read` function to expand the width of the list if an item is provided with more than 20 characters.

e.g.

```
=======================
     SHOPPING LIST     
=======================
         Apples        
        Bananas        
      Cranberries      
Basically lots of fruit
=======================
```

>hint: you don't need to do much
>use len() to find the length of a string
>use max() to find the maximum value in a list
>try a list comprehension


### 3. Numbering

Upgrade the `read` function to produce this output:

```
====================
      SHOPPING      
====================
       apples       (0)
      bananas       (1)
    cranberries     (2)
       dates        (3)
    elderberries    (4)
        figs        (5)
     grapefruit     (6)
====================
```

>hint: look up the [enumerate](https://docs.python.org/3/library/functions.html#enumerate) built in function.

