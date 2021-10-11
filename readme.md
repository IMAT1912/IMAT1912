# Lab exercises

In this series of lab exercises, we will build some very simple python applications.
The aim is not to deliver exhaustive coverage of the python ecosystem but to become familiar with python idioms and best practices by developing simple user interfaces for simple projects.
We will be developing very simple projects into high quality code.

The majority of your learning should take place outside of lab sessions where you are expected to experiment and practice to develop your understanding and experience with the language.
You are expected to practice between each session and to do your own research, going beyond the lab materials.
Ideally, looking for good questions to ask in the next session.

You are expected to become familiar with the python documentation.
Links are included to the appropriate parts of the documentation where possible.
Please follow them as necessary to get used to how the python documentation is written.

> Please do mention if there are missing links, I can easily add them in.

Most importantly, use the lab sessions to ask any questions you have.
Asking a good question will help to clarify your understanding and will help everyone in the room to confirm or challenge their understanding.

>I will answer your questions to the best of my ability in the lab sessions.
If you stump me (very likely), we can research the answer asynchronously and compare notes in the next session.

## Preparing a folder structure

We are going to be writing python scripts into files and executing them using the `python3` commandline programme.

>Your system may include a default `python` programme that is older.
>You can check the version like this:
>```bash
>python --version
>```
>If it is 3.8 or higher, use that.


You are encouraged to generate multiple files as you experiment each week.
Its important to organise your files carefully.
A folder structure like this is recommended.

```
IMAT1912-labs
├── lab_01
│   ├── lab_01.py
│   ├── experiment1.py
│   └── experiment2.py
└── lab_02
    ├── lab_02.py
    └── experiment3.py
```

In the above, the `lab_01.py` and `lab_02.py` files contain the code we build in the exercises.
All other files are for your use.

As you work through the exercise, try to keep the `lab_xx.py` files clean, only including the code in the exercise.
Feel free to experiment and produce more files, with snippets of code that you want to remember and write comments in those files to help you when you review the code later.

The assumption is that you will keep snippets and name them yourself according to your preferences.
In python a file is called a `module`.
From `PEP8` the official [style guide for python code](https://www.python.org/dev/peps/pep-0008/) : 
>*"Modules should have short, all-lowercase names. Underscores can be used in the module name if it improves readability."* 

`PEP8` is an important document, you should read it!

You should also be reading at least the first half of the [official python tutorial](https://docs.python.org/3/tutorial/index.html) which we will link to where appropriate.

# Lab summaries

## lab-01 - lists and strings

Working with **lists** and **strings** are essential python skills. 
In the first lab session we will create a simple *shopping list*.
The **list** will contain **strings** representing the items.

We introduce some control flow and play with a few common patterns for list and string manipulation.

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

## lab-02 - A simple `cmd` interface

In this lab we will expand the shopping list into a more fully featured application.
Users will be able to add, edit and delete items using a simple command line interface.

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

## lab-03 - Exception handling

The code we created above is fragile and prone to errors.
If our users provide a slightly wrong command, the programme crashes in a bad way.

In this lab, we will study the error messages and learn how to handle them.


## lab-04 - Introducing `tkinter`

We want to develop a graphical user interface (GUI) for our our shopping lis application.
In this lab we will introduce the `tkinter` module.

You challenge will be to build the GUI yourself.

## lab-05 - Introducing `tkinter`

In this lab we will use the `tkinter` module to build an interface for the shopping list application.

## lab_06: The datetime module - a countdown timer

Moving on from the shopping list, we will look at the datetime module and consider a simple coundown timer application.

## lab_07: A tkinter countdown timer

In this lab, we will build a `tkinter` GUI around the simple countodown timer.

## lab_08: Assignment support

The final two lab sessions are reserved for providing direct support with your assignment code. 

## lab_09: Assignment support

The final two lab sessions are reserved for providing direct support with your assignment code. 
