# Lab-01: Lists and strings

In this lab, we will build a partially functional *shopping list* application.
The programme will allow the user to add items to a list and will output the list to the terminal each time a new item is added.

>Ostensibly, this lab is about lists and strings. 
However, we will be using many different features of python together in order to build a simple programme.

>Going through this process as a python beginner may provide you with more questions than answers.
This is a good thing.

>Make sure you ask whenever something surprises you or if you want to clarify a generalisation.

> Also, elaborate on the provided examples and play around with python to check your understanding and see what you can do.


## Setup

Create a file `/path/to/IMAT1912-labs/lab_01/lab_01.py`.

>Remember, this is the folder structure you should be working in 
>```
>IMAT1912-labs
>└── lab_01
>    ├── lab_01.py
>    ├── experiment1.py
>    └── experiment2.py
>```
> Create your own experiments in additional files within the `lab_01` folder.


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

> your path will be different, depending on where you have created the `IMAT1912-labs` folder.

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

>To create an empty list, we could alternatively have called the built-in [list()](https://docs.python.org/3/library/functions.html#func-list) function and passed no argument to it.
>```python
>shopping = list()
>```
>[list()](https://docs.python.org/3/library/functions.html#func-list) converts any [iterable](https://docs.python.org/3/glossary.html#term-iterable) object to a list.
>Passing nothing into the function makes a list containing nothing.

>Both lists and strings are `iterable`.
>Iterable objects contain `members` which can be returned one at a time.
>They can be used in `for loop` and many other built in tools can consume iterables.
>We will see this in action later.

## Viewing the list

As we saw above, we can use the builtin [print()](https://docs.python.org/3/library/functions.html#print) method to output any values to the console.

>When showing print statements in these lab exercises, the printed output will usually be shown as a comment

```python
print(shopping)  # []
```

>print() can also output to files, or any other object pretending to be a file.
But its usually used for outputing to the terminal. 

## Add an item to the list

Let's remember to get some apples.

```python
shopping = []
shopping.append('apples')
```

Here we have added the string `'apples'` to the list. 
If we print the list now, we can see the fruits of our labour.

```python
print(shopping)  # ['apples']
```

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

>As alluded to above, strings are sequences of characters and are also iterable.
So this is perfectly normal in python.
>```python
>characters = list('apples')
>print(characters) # ['a', 'p', 'p', 'l', 'e', 's']
>```

## Retreiving items from the list

Starting with a few items in our list. 

```python
shopping = [
    'apples',
    'bananas',
    'clementines'
]
```

>Using consistent style is considered very important in the python community.
The [style guide for python code](https://www.python.org/dev/peps/pep-0008/) includes naming conventions and formatting guides for all python constructs, including multiline formatting for lists (see the end of the section on [indentation](https://www.python.org/dev/peps/pep-0008/#indentation)).

We can access the item at a given position using square bracket notation.

```python
shopping = [
    'apples',
    'bananas',
    'clementines'
]
print(shopping[0]) # 'apples'
```

We can also overwrite an item in the same way.

```python
shopping = [
    'apples',
    'bananas',
    'clementines'
]
shopping[2] = 'cherries'
print(shopping) # ['apples', 'bananas', 'cherries']
```

We can remove an item from the list by calling [list.pop()](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists).

```python
shopping = [
    'apples',
    'bananas',
    'clementines'
]
b = shopping.pop(1)
print(b)        # 'bananas'
print(shopping) # ['apples', 'clementines']
```

## Getting user input

Let's add a feature.
We want to get a new string from the user and add it to the list.

First, we get the string using the built in [input()](https://docs.python.org/3/library/functions.html#input) function.

The `input()` function takes one argument, a string which is used as a prompt for the user.

Try this:

```python
result = input('type something here -> ')
print(result) # 'this is what I typed'
```

So we can add an item to our list by putting the code together into a small programme.

```python
shopping = []
item = input('\nAdd an item: ')
shopping.append(item)
print(shopping)
```

>We added a newline character (`\n`) to make a bit of space for the user prompt.
If there is anything you don't understand above, ask NOW.
We are about to get a bit more complicated.

## An infinite loop

We are aiming to create a programme that can add more than one item to our list.
This will require calling the `input()` function multiple times to get each item from the user.

We could do something like this:

```python
shopping = []
print(shopping)
item = input('\nNew item: ')
shopping.append(item)
print(shopping)
item = input('\nNew item: ')
shopping.append(item)
print(shopping)
item = input('\nNew item: ')
shopping.append(item)
print(shopping)
item = input('\nNew item: ')
shopping.append(item)
print(shopping)
```
> notice we are now printing the list each time, so the user can see what they have already added.

The problem here is that we always ask the user for four items and we don't really know how many items our users will want to add.
If they want three or five items, they get a bad experience.

A typical solution to a problem like this is to create an infinite loop to continually ask the user for input and add the item to our list.
We then need to explicitly break out of the loop when the user tells us they are finished.
Obviously, we need to clearly specify how the user can do this.

First, we will create an infinite loop using a [while statement](https://docs.python.org/3/reference/compound_stmts.html#while).

```python
shopping = []
while True:
    print(shopping)
    item = input('\nItem: ')
    shopping.append(item)
```

Running this code works perfectly. 
We can add as many items as we like.
But the infinite loop creates a problem.
How do we stop?

> Use Ctrl + C to raise a `KeyboardInterrupt` error and break out of the programme.

So we will add a simple convention to our programme.
Using an [if](https://docs.python.org/3/tutorial/controlflow.html#if-statements) statement we can test whether the provided string is empty.
If the user enters a blank string, then we will [break](https://docs.python.org/3/tutorial/controlflow.html#break-and-continue-statements-and-else-clauses-on-loops) out of the loop.

```python
shopping = []
while True:
    print(shopping)
    item = input('\nItem (leave blank to exit): ')
    if not item: # checking for an empty string
        break
    shopping.append(item)
```

>Notice how we did this:
>
>```python
>if not item: # checking for an empty string
>    break
>```
>In python, most things can be tested for their [truth value](https://docs.python.org/3/library/stdtypes.html#truth-value-testing).
Things like empty lists and empty strings are considered false.
>The basic [boolean operations](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not) are `and`, `or` and `not`.


Now we have a fully working (if limited) application.
The user can add items to the list and end the programme by entering a blank value.

>We could take this one step further using the new [assignment expressions](https://docs.python.org/3/whatsnew/3.8.html#assignment-expressions) syntax (`:=`, known as the `walrus operator`).
>```python
>shopping = []
>print(shopping)
>while item := input('\nItem (leave blank to exit): '):
>    print(shopping)
>    shopping.append(item)
>```
>
>I also played with this approach for a while
>```python
>shopping = []
>
>def get_item(prompt):
>    """show the list and ask the user for a new item"""
>    print(shopping)
>    return input(prompt)
>
>while item := get_item('\nNew item (leave blank to exit): '):
>    shopping.append(item)
>```
>
>However, assignment expressions are a new feature in python 3.8 and I'm not sure if I like them yet.
>So I decided against it in the end.
>
>What do you think?
>

In python *simple is better than complex*, *flat is better than nested* and *readability counts* (see [PEP20](https://www.python.org/dev/peps/pep-0020/) or run `import this` in your code).


## Formatting the output

The printed list is functional but not very nice.
We can print each of the items on their own line instead.

```python
shopping = []
while True:
    print()
    for item in shopping:
        print(item)
    item = input('\nItem (leave blank to exit): ')
    if not item:
        break
    shopping.append(item)
```

>We use a [for](https://docs.python.org/3/tutorial/controlflow.html#for-statements) loop to iterate over each item in the list.
The `item` variable is assigned to each of the strings in our list turn.
All we need to do is `print` them.


This produces an output like this:

```
Item (leave blank to exit): apples

apples

Item (leave blank to exit): bananas

apples
bananas

Item (leave blank to exit): cherries

apples
bananas
cherries

```

This is still a bit plain.
We need to make the list stand out so it is easily readable.

### Adding a border

Objects of type `string` have lots of useful methods.
For example, [str.center](https://docs.python.org/3/library/stdtypes.html#str.center) will expand a string to a given number of characters by padding either side with spaces, or if one is provided, a character of your choice.

```python
print('SHOPPING LIST'.center(20, '=')) # '===SHOPPING LIST===='
```

We can also use an [f-string](https://docs.python.org/3/tutorial/inputoutput.html#formatted-string-literals) (or `formatted string literal`) to create a string which is includes interpolated variables.

```python
item = 'apples'
print(f'|{item.center(18)}|') # '|      apples      |'
```
>notice we didn't provide a padding character so the default character was used (a space).

In python, strings can be multiplied by integers to repeat the string multiple times.
Its strange but sometimes useful.


```python
print('=' * 20) # '===================='
```

Study the above examples carefully and do your own experiments. 

Let's put these pieces together to format the list items to be centrally aligned within a border.


```python
shopping = []
while True:
    print()
    print('SHOPPING LIST'.center(20, '='))
    for item in shopping:
        print(f'|{item.center(18)}|')
    print('=' * 20)
    item = input('\nItem (leave blank to exit): ')
    if not item:
        break
    shopping.append(item)
```

Now our output is more spaced out:

```

===SHOPPING LIST====
====================

Item (leave blank to exit): apples

===SHOPPING LIST====
|      apples      |
====================

Item (leave blank to exit): bananas

===SHOPPING LIST====
|      apples      |
|     bananas      |
====================

Item (leave blank to exit): cherries

===SHOPPING LIST====
|      apples      |
|     bananas      |
|     cherries     |
====================

Item (leave blank to exit): 
```

Our code is beginning to become cluttered.
Let's end the first lab by writing some [functions](https://docs.python.org/3/tutorial/controlflow.html#defining-functions).

Functions are great for keeping our code neat and clear. 
Consider the code above and compare it to the following.

```python
shopping = []

def print_list(a_list):
    print()
    print('SHOPPING LIST'.center(20, '='))
    for item in a_list:
        print(f'|{item.center(18)}|')
    print('=' * 20)


while True:
    print_list(shopping)
    item = input('\nItem (leave blank to exit): ')
    if not item:
        break
    shopping.append(item)
```

Try running the programme with our new `print_list` function.

## Conclusion

In this first lab, we have introduced a lot.
- comments
- assignment
- boolean operations (`not`)
- iteration (`for`)
- control flow (`if` and `while`, `break`)
- functions

We have worked with `strings`, including:
- `print()`
- `str.center()`
- f-strings
- string multiplication
- `input()`

We have worked with `lists`, including:
- list literals
- `list()`
- indexing
- `list.append()`
- `list.pop()`

There is a lot here so don't worry if you are feeling overwhelmed.
Spend some time thinking about what we have done.
If there is any part that you feel needs clariication, feel free to ask questions.


## Challenges

Upgrade the `print_list` function to meet the following requirements.

### 1. Handle an empty list

Print "EMPTY" inside the list when its empty (not otherwise).

```
===SHOPPING LIST====
       EMPTY        
====================
```

>hint: empty lists are considered False.

### 2. Dynamic formatting

What happens if you add an item to the list which is wider than 20 characters?

Upgrade the `print_list` function to detect the longest list item and expand the width of the list if an item is provided with more than 20 characters.

e.g.

```
=======SHOPPING LIST=======
|basically, a lot of fruit|
|          apples         |
|         bananas         |
|         cherries        |
===========================
```

>hint: you don't need to do much
>use len() to find the length of a string
>use max() to find the maximum value in a list
>maybe try a list comprehension


### 3. Numbering

Upgrade the `read` function to produce this output:

```
=======SHOPPING LIST=======
|basically, a lot of fruit| (0)
|          apples         | (1)
|         bananas         | (2)
|         cherries        | (3)
===========================
```

>hint: f-strings are good for this. Also, look up the [enumerate](https://docs.python.org/3/library/functions.html#enumerate) built in function.

I will release a video work through of the lab and a solution before next week.