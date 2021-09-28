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

# Lab summaries

## [lab-01](./lab_01/lab_01) - lists and strings

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
