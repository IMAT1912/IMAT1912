# Lab-03: Exception handling

In the previous two lab sessions we have built a shopping list application based on a simple python list containing strings representing the items on the list.

```python
['apples', 'bananas', 'cherries']
```

We created a `ShoppingList` class to contain our list and gave our class methods for manipulating the list items.

```python
fruit = ShoppingList()
fruit.add('apples')
fruit.add('bananas')
fruit.add('cherries')
print(fruit)
```

Generating formatted output on the console

```
====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
|      cherries      | (2)
======================
```

Finally, we inherited functionality from the `cmd` module to give our class a command line interface (CLI) specifying the commands `'quit'`, `'add'`, `'edit'` and `'delete'`.
We also inherited the `'help'` command.

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
```

However, as you may have discovered along the way, the code we created is fragile and prone to errors.
If our users provide a slightly wrong command, the programme crashes in a bad way.

```
shopping: edit apples
Traceback (most recent call last):
  File "lab_03.py", line 74, in <module>
    shopping.cmdloop()
  File "/usr/lib/python3.8/cmd.py", line 138, in cmdloop
    stop = self.onecmd(line)
  File "/usr/lib/python3.8/cmd.py", line 217, in onecmd
    return func(arg)
  File "lab_03.py", line 52, in do_edit
    index, new_value = args.split(maxsplit=1)
ValueError: not enough values to unpack (expected 2, got 1)
```

This is not acceptable for our users, we never want them to see this technical stuff.

In this lab, we will study the error messages and learn how to handle them.
But first, we presented some other challenges at the end of the last session.

## Setup

As usual, you are reminded to keep your code well organised.

Create a file `/path/to/IMAT1912-labs/lab_03/lab_03.py`.

>Remember, this is the folder structure you should be working in 
>```
>IMAT1912-labs
>├── lab_01
>│   ├── lab_01.py
>│   ├── experiment1.py
>│   └── experiment2.py
>├── lab_02
>|   ├── lab_02.py
>|   └── experiment3.py
>└── lab_03
>    ├── lab_03.py
>    └── experiment4.py
>```
> again, create your own experiments in additional files within the `lab_03` folder.

Start with the code we produced last week.

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

>Our code is getting long, so we will try to work on it piece by piece.
In this lab, when we indicate a function should be updated, you should make the changes to your code.
Be very careful to avoid getting confused as we will not be showing the full code listing again until the end.

## 1. Implement `'load'` and `'save'` commands

When we start running our programme, the list is always empty.
This is not very useful.
All our work is lost when the programme exits.

We want the ability to write the list into a file to that it can be loaded back into our programme.
The file can be very simple, containing one item per line.

>Opening files to access or edit their content is easy.
The built in [open()](https://docs.python.org/3/library/functions.html#open) function is the general purpose method for doing this.
>However, we will use the [pathlib module](https://docs.python.org/3/library/pathlib.html).
In particular, the [Path](https://docs.python.org/3/library/pathlib.html#concrete-paths) class which allows us to interact with files in a more elegant way. The equivalent method is [Path.open()](https://docs.python.org/3/library/pathlib.html#pathlib.Path.open).

Add this to the top of your file.

```python
from pathlib import Path
```

`Path` objects represent locations in a filesystem and they have convenient methods for navigating around and interacting with the files and folders they point at.

### A `'save'` command

We are intending for the user to enter the `save` command with a simple filename to specify the text file into which we will write the data to be saved.

For example, if the user enters this command:
```
shopping: save shopping.txt
```
This would call our method like this:
```python
stop = self.do_save('shopping.txt')
```


>There is no need for the file to have a `.txt` extension (we could have chosen a `.shopping` extension for example, or we could have added the extension programmatically), but this is what we will use in the example.

Like the `do_add`, `do_edit` and `do_delete` methods, our `do_save` method will take a single string argument.

Add the following `do_save` method to your class:

```python
def do_save(self, arg):
    """Save data to the specified file"""
    data_path = Path(arg)
    with data_path.open('w') as f:
        for item in self.items:
            print(item, file=f)
```

The method is fairly simple.
It opens the specified file for writing, loops over our list, writes each item into the file and closes the file.
Let's look in more detail.

In the first line we instantiate a `Path` object with `arg` as the only argument.

```python
data_path = Path(arg)
```

>In the above example, this is equivalent to the following:
>```python
>data_path = Path('shopping.txt')
>```
>It creates an object that represents the specified location on disk.
>
>`pathlib` has many features which make it convenient and flexible.
>For example we could easily store all the list files in a `'data'` directory using the `joinpath` method like this.
>```python
>data_folder = Path('data') # representing a folder, 'data'
>data_path = data_folder.joinpath(arg) # joins the path in an OS-specific way.
>```

The next line is a [with statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement) that manages access to a file object.

```python
with data_path.open('w') as f:
    # do something with `f`
```

This needs some explanation.

The `Path.open()` method returns a reference to a file object as defined in the [IO module](https://docs.python.org/3/library/io.html#module-io) (in particular, an [io.TextIOWrapper](https://docs.python.org/3/library/io.html#io.TextIOWrapper) object in this case).
We are specifying the `mode` as `'w'` to create a file object specifically for writing text.
>The equivalent mode for reading text files is `'r'`.


The file object is a [context manager](https://docs.python.org/3/library/stdtypes.html#context-manager-types) and this is why we can use the `with` statement to manage our access to the file.

>file objects can be created like this
>```python
>f = data_path.open('w')
>```
>and they must be closed when writing is finished to release the file lock.
>```python
>f.close()
>```

Notice the [with statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement) is managing access to the file object itself.

```python
with data_path.open('w') as f:
    # do something with `f`
```

This is very common usage when accessing file objects.
It makes sure that the file is only open and accessible (via the variable `f`) within the indented block.
Once the `with` block ends, the file is automatically closed.

>The `with statement` above is equivalent to this:
>```python
>f = data_path.open('w')
>try:
>    # do something with `f`
>finally:
>   f.close()
>```
>
>You will often see examples using the [open()](https://docs.python.org/3/library/functions.html#open) command like this:
>```python
>with open(filename, 'w') as f:
>   # do something with f
>```
>We are using the [pathlib.Path.open()](https://docs.python.org/3/library/pathlib.html#pathlib.Path.open) method which is functionally exactly the same.
>```python
>with Path(filename).open('w') as f:
>   # do something with f
>```
>The only difference is that the `Path` object is responsible for specifying the location of the file.

The next two lines inside the `with` block should be familiar, we are looping over the `self.items` list, assigning the `item` variable to each of our strings in turn and calling the familiar `print()` function. 

```python
for item in self.items:
    print(item, file=f)
```

When we print each item, we specify our `f` variable as the `file` argument to the `print()` function.
This (as you may have guessed) outputs the string into our file rather than to the console.

As described above, the file is closed automatically when the `with` block ends.

Try adding a few items to the list and calling the new `'save'` command.

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

shopping: save shopping.txt

====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
======================

```

You should see that a new file `'shopping.txt'` has been created in your file system.

>The location of the file will be relative to the current working directory, so will be created wherever you call the programme from (i.e. within the `IMAT1912-labs/lab_03/` folder).

check the content of the file, it should include one line per item.

```
apples
bananas
```

Now, exit the programme so we can start from an empty list.

### A `'load'` command

Similarly, we can read the data from our file like this.

```python
def do_load(self, arg):
    """Load data from the specified file"""
    data_path = Path(arg)
    data_path.touch(exist_ok=True)
    with data_path.open('r') as f:
        self.items = f.read().splitlines()
```

The method will be called in a similar way, passing the filename to load as the only argument.

```
shopping: load shopping.txt
```

We start by creating a reference to the `Path` object.

```python
data_path = Path(arg)
```

We can't read from non-existent files.
So if the file doesn't already exist, we want a blank file to be created.
We do this by calling `Path.touch` to create an empty file.

```python
data_path.touch(exist_ok=True)
```

>We pass in `exist_ok=True` as an argument to indicate that if the file already exists, that's OK.

We then do the familiar `with` statement to open the file for reading (using the `'r'` mode this time).

```python
with data_path.open('r') as f:
    # read from `f`
```

We read in all the data using [f.read()](https://docs.python.org/3/library/io.html#io.TextIOBase.read) (which returns a string) and split it into lines using [str.splitline()](https://docs.python.org/3/library/stdtypes.html#str.splitlines).
The result is a list of strings which we directly assign to our `self.items` attribute, overwriting any existing data.

```python
self.items = f.read().splitlines()
```

Now we can test it out by running the programme again.

```
My awesome shopping list app

shopping: load shopping.txt

====SHOPPING LIST=====
|       apples       | (0)
|      bananas       | (1)
======================
```
Great! 
The file was loaded successfully.

Now you can try editing the file manually or by using the `'add'`, `'edit'` and `'delete'` commands followed by the `'save'` command.

## 2. Load and save your list automatically

We want to avoid the need for the user to issue the `'load'` and `'save'` commands every time they use the app.
To do this, we will need to specify a default filename for the list and automatically load and save to that file.

Much like the `title` attribute we gave them last time, each of our `ShoppingList` instances needs to point to a file.
We can give them a `path` attribute which can **be** a `pathlib.Path`.

Update your `ShoppingList.__init__()` method as follows:

```python
def __init__(self, title='shopping list', filename="shopping.txt"):
    self.items = []
    self.title = title
    self.intro = '\nMy awesome shopping list app'
    self.prompt = '\nshopping: '
    self.path = Path(filename)
    super().__init__()
```

Now we need to load data from the file and print the list before we prompt the user for any input.
We can do this in the [Cmd.preloop](https://docs.python.org/3/library/cmd.html#cmd.Cmd.preloop) method.

```python    
def preloop(self):
    self.path.touch(exists_ok=True)
    with self.path.open('r') as f:
        self.items = f.read().splitlines()
    print(self)
```

We are printing the object after loading in the `preloop()` method because this is *not* calling `do_load()` and therefor *not* calling our `postcmd()` method.

Notice that, because we are repeating much of the `do_load` functionality, we can split out the common code into a `_load()` utility function that will do the job of loading data from any given `Path` object into our `self.items` attribute.

```python
def _load(self, path):
    path.touch(exist_ok=True)
    with path.open('r') as f:
        self.items = f.read().splitlines()

def do_load(self, arg):
    """Load data from the specified file"""
    data_path = Path(arg)
    self._load(data_path)
    
def preloop(self):
    self._load(self.path)
    print(self)
```

>It is important to adhere to `DRY` (`D`on't `R`epeat `Y`ourself) principles when dealing with this kind of thing.
There should be only one place where we do the job of reading data from a file.

We can do a (very) similar thing in the [postloop()](https://docs.python.org/3/library/cmd.html#cmd.Cmd.postloop) method to automatically save our list to the default file when the programme ends.

```python
def _save(self, path):
    with path.open('w') as f:
        for item in self.items:
            print(item, file=f)
        print(f'saved to {path.resolve()}')

def do_save(self, arg):
    """Save data to the specified file"""
    data_path = Path(arg)
    self._save(data_path)

def postloop(self):
    self._save(self.path)
```

This shouldn't be much of a surprise.
We have created the `_save()` method to carry out the common task of actually saving data to a provided path.
We call this method in the `do_save` method (to allow users to use the `'save'` command) and in the `postloop()` method (to automatically save the data on exit).

Try it out.
You should now be able to maintain a list.

>Consider whether we actually need the `'save'` and `'load'` commands and how they should be used.
>
>If the user loads a file, should the data be automatically saved back to that same file on exit or to the default file?
Think about how you would implement this.
>
>Sometimes it better to remove functionality that isn't crystal clear.

## 3. Add a `'clear'` command

OK, hopefully this is an easy one. 

```python
def do_clear(self, args):
    """Clear the list"""
    self.items = []
```

Our app is looking pretty good.
But it relies on the user getting commands perfect.

# Handling exceptions

Let's turn our attention to what happens when things aren't so perfect.
Our code is fragile, the commands we have defined need perfect input in order to work properly.
If the user enters invalid arguments, our programme can crash.

>Have a scan through [Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html) in the python tutorial.
In order to write robust python code, you will need to understand this in some detail.

We will start by handling errors in the `do_edit` method because it is the most complicated. 
Then, we will generalise our solution and apply it to the other functions as necessary.

## ValueError: not enough values to unpack

Start the app and clear the list.

```
shopping: clear

====SHOPPING LIST=====
|       EMPTY        |
======================
```

Now do this:

```
shopping: edit apple
```

The command is invalid because we need two values, an integer index followed by a new value for the item in question.
The resulting error tells us what happened

```
Traceback (most recent call last):
  File "lab_03.py", line 95, in <module>
    shopping.cmdloop()
  File "/usr/lib/python3.8/cmd.py", line 138, in cmdloop
    stop = self.onecmd(line)
  File "/usr/lib/python3.8/cmd.py", line 217, in onecmd
    return func(arg)
  File "lab_03.py", line 80, in do_edit
    index, new_value = args.split(maxsplit=1)
ValueError: not enough values to unpack (expected 2, got 1)
```
>Your output may be slightly different, but you can trace through your error in exactly the same way.

Learning to read error messages is a crucial skill.
Study the output and try to understand what it is saying.

Let's break it down.
The first line is a simple introduction to the `traceback` that follows.

```
Traceback (most recent call last):
```

This contains an important clue.
We might want to look at the last item to see what really went wrong.

The following four entries show the key lines of code that led to the error and should help us to diagnose and fix the problem.

Each entry looks something like this:

```
  File "lab_03.py", line 95, in <module>
    shopping.cmdloop()
```

This is the entry point to the problem within our code.
We called the `cmdloop()` method on line 95 of `lab_03.py` (your line may be slightly different) and it had not finished executing when the error occured. 

But the error occurred deeper in the code.
In fact, the traceback passes through the `cmd` module code before it gets back to our code.

```
  File "/usr/lib/python3.8/cmd.py", line 138, in cmdloop
    stop = self.onecmd(line)
```
The `cmdloop()` method calls `onecmd(line)` internally each time the user enters a command.
We can see that this occurred on line 138 in the `cmd.py` module (and that my system is running python 3.8!).
This code eventually leads to our `do_edit` method being called.

The next entry in the error message shows another step in the process.

```
  File "/usr/lib/python3.8/cmd.py", line 217, in onecmd
    return func(arg)
```
Again, this is part of the `cmd` library.
The `onecmd()` method finds the requested function (`func`, in this case refers to our `do_edit` function).
It calls the function and passes in the provided arguments (`arg`).
We discussed this last week.

So finally, the error trace points to our function.

```
  File "lab_03.py", line 80, in do_edit
    index, new_value = args.split(maxsplit=1)
```

This is the "most recent call last" alluded to above.
The offending expression (line 80) is shown very clearly.
We can see that we tried to split the `args` string into two variables, `index` and `new_value`.

>The error tells us the exact file and line where the problem occurred.
>
>```python
>def do_edit(self, args):
>    """replace the item at the given index"""
>    index, new_value = args.split(maxsplit=1) # <-- here
>    index = int(index)
>    self.items[index] = new_value
>```
>This is the offending function
>The error is caused because in this case, the value of `args` is `'apple'` so the result of `args.split(maxsplit=1)` is a list with only one element.
>
>When we attempt to [unpack](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences) the list (`['apple']`) into two variables, a `ValueError` exception is raised.

The final line of the error trace shows the actual exception that was raised by this code when the user entered only one argument.

```
ValueError: not enough values to unpack (expected 2, got 1)
```

This is the crucial information we can use to [handle the error](https://docs.python.org/3/tutorial/errors.html#handling-exceptions).
We need to wrap the dangerous code in a `try` statement with an `except` clause to handle the `ValueError`.

>We don't always want to handle exceptions, sometimes they are useful in our code to indicate that we have done something wrong.

In this case, there isn't much we can do if the user enters a bad command.
We cannot avoid this situation and we want to handle it gracefully.
So we will simply print out a message and stop processing the request.

```python
def do_edit(self, args):
    """replace the item at the given index"""
    try: 
        index, new_value = args.split(maxsplit=1)
    except ValueError:
        print("invalid request - two values needed")
        return 
    index = int(index)
    self.items[index] = new_value
```
Everything inside a `try` statement will be subject to the error handler. 
The `except` clause will only run if the given error is raised in the code which is *protected* by the `try` statement.
`ValueError` is a built-in object that is specifically designed for this particular task.

>We can also raise our own `ValueError` if we want to indicate e.g. that an argument provided to a function has an invalid value.

Now, restart the app and try again. 
You should get a helpful message and, crucially, the app no longer crashes but continues processing commands.
Because we handled the error, our code continues to run without a problem.

```
My awesome shopping list app

shopping: edit apple
invalid request - two values needed
```

Our users can now identify what happened and correct the mistake or enter different command.

## ValueError: invalid literal for int()

The index provided by the user needs to be an integer. 
Try entering an invalid index like this:

```
My awesome shopping list app

shopping: edit apple apple
```
```
  File "lab_03.py", line 99, in <module>
    shopping.cmdloop()
  File "/usr/lib/python3.8/cmd.py", line 138, in cmdloop
    stop = self.onecmd(line)
  File "/usr/lib/python3.8/cmd.py", line 217, in onecmd
    return func(arg)
  File "lab_03.py", line 85, in do_edit
    index = int(index)
ValueError: invalid literal for int() with base 10: 'apple'
```

We can see a familiar pattern here, the traceback shows tha path our code took before the exception was raised.

>Notice the last entries
>```
>File "lab_03.py", line 85, in do_edit
>  index = int(index)
>ValueError: invalid literal for int() with base 10: 'apple'
>```
>The line is now 85, because we added some lines in the previous step.

As expected, the attempt to do `int('apple')` raised another `ValueError` exception.
We can handle it in a similar way by putting the code that raises the exception in a `try` block and adding an `except` clause to handle the specific exception.

```python
def do_edit(self, args):
    """replace the item at the given index"""
    try:
        index, new_value = args.split(maxsplit=1)
    except ValueError:
        print("invalid request - two values needed")
        return
    try:
        index = int(index)
    except ValueError:
        print("invalid request - index must be an integer")
        return
    # we only get here if there are no errors
    self.items[index] = new_value
```

Now we get a nicer message and again, the app doesn't crash.

```
My awesome shopping list app

shopping: edit apple apple
invalid request - index must be an integer
```

Now, we have just one more serious problem.

## IndexError: list assignment index out of range

What if the index argument is a valid integer, but its too big? 
What if we ask to edit a value beyond the end of our list?

Try this:

```
shopping: edit 10 apple
```
```
Traceback (most recent call last):
  File "lab_03.py", line 103, in <module>
    shopping.cmdloop()
  File "/usr/lib/python3.8/cmd.py", line 138, in cmdloop
    stop = self.onecmd(line)
  File "/usr/lib/python3.8/cmd.py", line 217, in onecmd
    return func(arg)
  File "lab_03.py", line 90, in do_edit
    self.items[index] = new_value
IndexError: list assignment index out of range
```

>Scan backwards from the end:
>```
>  File "lab_03.py", line 90, in do_edit
>    self.items[index] = new_value
>IndexError: list assignment index out of range
>```
>OK, it looks like we tried to set a value (assign) to an index beyond the size of the list.

We can't reference `self.items[10]` when the list is empty.
When we try, an `IndexError` exception is raised.

By now we know the drill.
We wrap the dangerous line of code in another `try statement`.

```python
def do_edit(self, args):
    """replace the item at the given index"""
    try:
        index, new_value = args.split(maxsplit=1)
    except ValueError:
        print("invalid request - two values needed")
        return
    try:
        index = int(index)
    except ValueError:
        print("invalid request - index must be an integer")
        return
    try:
        self.items[index] = new_value
    except IndexError:
        print(f"invalid request - we only have {len(self.items)} items")
        return
```
>Notice we gave a bit more information in this error by checking the length of the array.
>```python
>print(f"invalid request - we only have {len(self.items)} items")
>```
>We could do even better, like this:
>```python
>print(f"invalid request - try one of {list(range(len(self.items)))}")
>```
>This actually lists out all the valid indices.
>
>Choosing good error messages is difficult.
>It requires thinking about who the user is and what information they need.

Our code is becoming rather cluttered now. 
But the last few errors will give us some opportunity to refactor.

Try entering this:

```
shopping: delete apple
```
```
Traceback (most recent call last):
  File "lab_03.py", line 108, in <module>
    shopping.cmdloop()
  File "/usr/lib/python3.8/cmd.py", line 138, in cmdloop
    stop = self.onecmd(line)
  File "/usr/lib/python3.8/cmd.py", line 217, in onecmd
    return func(arg)
  File "lab_03.py", line 99, in do_delete
    index = int(index)
ValueError: invalid literal for int() with base 10: 'apple'
```

This is looking very familiar.
Let's create a utility function to validate a provided index.
It will need to:
- convert the string to an integer
- confirm the integer is in the appropriate range
- raise useful error messages that can be shown to the user

```python
def validate_index(self, arg):
    """Extract an index from the given string argument
    raises useful error messages
    """
    try:
        index = int(arg)
    except ValueError:
        raise ValueError(f"{arg!r} should be an integer")
    try:
        _ = self.items[index]
    except IndexError:
        raise IndexError(f"{index} not in {list(range(len(self.items)))}")
    return index
```

The above function includes two `try` blocks. 
The `int(arg)` conversion will raise a `ValueError` exception if the argument cannot be converted to an integer.
Notice that it raises its own ValueError with a different message.

The second `try` block wraps an attempt to access the given index in the `self.items` list. 
This will raise an `IndexError` if the index cannot be used in our list.
Again, we raise our own IndexError if this causes a problem and we add our own, more useful message.

We can use our function in the `do_delete` method like so.

```python
def do_delete(self, index):
    """delete the item at the given index"""
    try:
        index = self.validate_index(index)
    except (ValueError, IndexError) as error:
        print(f'invalid command - {error}')
    else:
        del self.items[index]
```

Now we are handling both `ValueError` and `IndexError` exceptions with the same handler.
All we do is print the error message that came with the exception.

We also added an `else` clause which only executes if the `try` block successfully completes without raising any exceptions.

>This syntax allows us to handle multiple exception types
>```python
>except (ValueError, IndexError) as e:
>```
>The `as error` syntax gives our handler access to the exception that was raised.

We can expand the error handling code with another utility method for splitting the args into an index and a string.

```python
def parse_args(self, args):
    try:
        index, new_value = args.split(maxsplit=1)
    except ValueError:
        raise ValueError(f"two values needed, only got {args!r}")
    return self.validate_index(index), new_value
```

This is pretty nice because if the call to `validate_index` raises an error, it will be raised here.
This allows us to greatly simplify the `do_edit` error handling.

```python
def do_edit(self, args):
    """replace the item at the given index"""
    try:
        index, new_value = self.parse_args(args)
    except (ValueError, IndexError) as error:
        print(f'invalid command - {error}')
    else:
        self.items[index] = new_value
```

Our code is getting quite long now, but it is well-organised and the intent is clear.
Read through the code and make sure you understand each method individually.

## Challenges

Study the following programme and understand what it does.

```python
print('remember to get some "apples"')
print('remember to get some "bananas"')
print('remember to get some "cherries"')
```

There is repetition here.
Can you refactor it?
>Try to use a pythonic style.

Next week we will looking at the `tkinter` GUI library.

Research the `tkinter` system and try to build something with it.