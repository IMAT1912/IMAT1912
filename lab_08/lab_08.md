# Lab_08: Getting started with your assignment

Creating your own code is a very different process from following a set of instructions and following examples.
It requires that you think ahead to imagine a solution before you try to implement it.

What the lab materials don't show is the trial and error which is a necessary part of the creative design (and redesign) process that produced the demo applications.
The code provided in the labs is the carefully presented end result of a somewhat more complex process in which mistakes are made and the code is refactored into a clean design.

>Obviously, there are good reasons we don't present you with the real development process in the lab materials.
>There are hints of the process in some of the solution videos as the code sometimes takes a slightly different path to the lab exercises.
>However, if, when recording, I go too far off the main path or make serious mistakes, I will edit it out to keep the video short and clear.

The reality of development is that code evolves gradually and needs refactoring often.
You may hit challenges that may completely stump you.
These next three weeks of lab sessions are your opportunity to engage with your tutor to discuss the problems and possible solutions you have encountered.

To some extent, you will be able to follow the pattern presented in the module materials.
In which case, the basic infrastructure for your application might look something like this:

```python
# import what you need
import tkinter as tk

# Have a main application class
class ChooseABetterApplicationName(tk.Tk):

    # You need a constructor method to setup the application
    def __init__(self):
        super().__init__()
        self.title('choose a title')

        # create some widgets here

        # layout your widgets here

    # add methods to do things here

# create an instance
app1 = ChooseABetterApplicationName()

# start the tkinter main loop
app1.mainloop()
```

>Obviously, you need to choose a name and title for your application and actually put some code in place.

These are the (slightly reworded) core requirements for the two assignment options.

| | Alarm clock | Address book |
|---|---|---|
| 1. | Users can see the current time | Users can add a new contact. |
| 2. | Users can set an alarm, for a time in the future. | Users can view an alphabetical list of contacts |
| 3. | A sound will play when the set time is reached. | Users can select a contact to view the details |
| 4. | Users can dismiss the alarm to stop the sound. | Users can edit/delete existing contacts |

## An approach

You might think about the process like this:

- consider the data your application will manage
- design your interface (on paper?)
- add widgets into the app
- layout the widgets using `grid()`
- make the widgets functional and interactive

The above steps can (and should) be repeated many times as your application evolves towards a solution you are satisfied with.
If the project feels too big, take small steps by considering one requirement at a time.
If a requirement is too complex, break it up into smaller pieces and follow the process for only a small piece of the requirement.

### Data

Thinking about data is very important.
In the shopping list application, we had a simple `list` of strings which provided our core data.
In the countdown and stopwatch applications, we stored `datetime` and `timedelta` objects to keep track of the state of the application.
It is a good idea to think carefully about the basic data your application will need to manage. 

In both cases, there are optional features to save the state to a file and reload it.
It may be useful to think about what kind of information you need to save. 

### User interface design

It can be useful to spend some time imagining the user interface you will build.
In particular, drawing the design on paper (or using diagramming tools or whatever you feel comfortable with) and thinking about how the user will achieve the basic goals (listed in the table above).

The first phase of a project might include a few sketches to develop prototype designs.
Thinking carefully about how each of the core features would work.
This phase should result in a basic plan of how the application will look and how it should respond to the core user interactions.

However, as your project develops, it is often the case that you will discover problems that you hadn't thought of up front.
So don't be afraid to modify your design over time, if necessary.

### Adding widgets

In our demo applications, we typically created widgets in the `__init__` method.
You should think about what widgets you will need when the programme starts and create these in the same way we did.
You may need to refer to these widgets in other methods, in which case you can store a reference to the widget on `self`.

Most of the time, it will look something like this.

```python
self.label = tk.Label(text="this is a label")
```
We have used widgets such as `Label`, `Button`, `Entry`, `Listbox`, `Frame` and `Canvas` in the demo applications.
These should be sufficient for a simple interface. 
Feel free to explore the wider set of available widgets.

### Layout

Using `grid()` for layout is recommended because it is the most flexible approach.
Specifying `row` and `column` values (and `rowspan` and `columnspan`) for each call to grid should be simple if you create a diagram of the interface as we did in the example applications.

<table class="app" style="text-align: center; min-width: 400px">
    <tr>
        <td colspan=2>Label</td>
    </tr>
    <tr>
        <td class="entry">Entry</td>
        <td></td>
    </tr>
    <tr>
        <td rowspan=3 style="min-width: 220px;" class="entry">Listbox</td>
        <td>Button</td>
    </tr>
    <tr>
        <td>Button</td>
    </tr>
    <tr>
        <td>Button</td>
    </tr>
</table>

Try to convert your diagram into a list of the widgets you need and work out exactly how many columns and rows you need in your grid.

| widget        | row | column | rowspan | columnspan |
|:--------------|:---:|:------:|:-------:|:----------:|
| label         | 0   | 0      | -       | 2          |
| entry         | 1   | 0      | -       | -          |
| listbox       | 2   | 0      | 3       | -          |
| add button    | 2   | 1      | -       | -          |
| update button | 3   | 1      | -       | -          |
| delete button | 4   | 1      | -       | -          |


For more complex designs, you may need a `tk.Frame` widget.


<table class="app">
    <tr>
        <td style="text-align: center;" colspan=2>Shopping List</td>
    </tr>
    <tr>
        <td class="entry"></td>
        <td style="width: 20px">Add</td>
    </tr>
    <tr>
        <td colspan=2 style="background-color: #aaa; height: 100px; text-align: center; font-size: 2em;">
            tk.Frame
        </td>
    </tr>
    <tr>
        <td></td>
        <td>Clear</td>
    </tr>    
</table>

This will allow you to create a new grid inside the frame which you position in a cell (or spanning multiple cells) of the main grid.

>In lab_05 we actually designed a custom compound *shopping item* widget which was wrapped inside a `tk.Frame` widget.
We then created another `tk.Frame` which acted as a container for our shopping items.

Remember to use `columnconfigure` and `rowconfigure` to allocate any spare space (if you allow resizing the window).
Also set `minsize` (and/or `maxsize`) to restrict resizing as appropriate.
Combining this with careful use of the `sticky` argument to `grid()` can create really nice interfaces.

### Adding functionality

Widgets on their own don't usually do much.
We have covered a number of ways to make your application "do things".
Simple examples include setting methods as `command` arguments to `tk.Button` widgets.
When a button is pressed, it's `command` will be executed.

```python
import tkinter as tk

class ChooseABetterApplicationName(tk.Tk):

    def __init__(self):
        super().__init__()
        self.title('choose a title')
        self.button = tk.Button(text="click me", command=self.click_handler)
        self.button.grid()

    def click_handler(self):
        print("I'm handling the click")

app1 = ChooseABetterApplicationName()
app1.mainloop()
```

>Your handlers can be named however you like and can interact with your data and/or the user interface.

Another way we can make things happen is via events.
We saw this in lab_05.
[The documentation for events](https://anzeljg.github.io/rin2/book2/2405/docs/tkinter/event-sequences.html) is a bit slim, but with some resaerch (and help from tutors) you should be able to trigger your code based on [a wide range of event types](https://anzeljg.github.io/rin2/book2/2405/docs/tkinter/event-types.html).

```python
import tkinter as tk

class ChooseABetterApplicationName(tk.Tk):

    def __init__(self):
        super().__init__()
        self.title('choose a title')
        self.entry = tk.Entry()
        self.entry.grid()
        self.entry.bind("<KeyPress>", self.key_handler)

    def key_handler(self, event):
        print(f"The {event.keysym} key was pressed.")

app1 = ChooseABetterApplicationName()
app1.mainloop()
```

Your event handler methods must accept an `event` argument which contains information about the event.
In the example we can see the `KeyPress` event of the entry has been bound to the `key_handler` method.
The method established which key was pressed and prints it to the terminal. 

Another way we have seen to control the application behaviour is to use `self.after` to trigger a method to execute after a given number of milliseconds.

```python
import tkinter as tk

class ChooseABetterApplicationName(tk.Tk):

    def __init__(self):
        super().__init__()
        self.title('choose a title')
        self.entry = tk.Entry()
        self.entry.grid()
        self.update()

    def update(self):
        print(f"I'm updating now")
        self.after(1000, self.update)

app1 = ChooseABetterApplicationName()
app1.mainloop()
```

One interesting thing this allows, as in the above example, is for a continuous update loop to be established.

## OK, But how do I start?

Pick one feature. 
The smaller the better.
Don't start with a complicated feature.
Does your app need any widgets?
Just displaying something on the screen would be a good start. 

Pick one, or maybe two widgets.
Decide where they go.
Build an application, based on the template provided.
Add your widget(s).
Call `grid()` on each widget and put them where you want them.

Once you have something on the screen, adapt it until you are happy.
Does it need to be moved?
Is the font OK?

What is the next step?
Usually, you will know what to do next.
If you don't know what to do next, ask your lab tutor.

Think.
How should the user interact with your application?
How should the interface respond?
Do you need to store some data?
Where will it go?
Do you need more widgets?

Repeat until your application is finished.
Tick off the requirements one-by-one.
