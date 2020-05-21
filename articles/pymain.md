# What does `if __name__ == "__main__"` do?

In Python, you might run across code like this:

```py
def process_data(data):
    # Processes our data
    return data

if __name__ == "__main__":
    data = read_data()
    process_data(data)
```

That last bit with `if __name__ == "__main__"` is pretty confusing. Here's what's going on.

*Simplest version*: That's the code that will run when you run that Python script on the command line.

When Python runs, it defines some special variables. `__name__` is one of these, along with others like `__file__`. `__name__` is the name of the currently running _module_. A Python module is the set of Python code in one file.

In your Python code, you often import other modules, like this:

```py
import random
random.random()
```

Inside the code in `random`, `__name__` is defined as "random". If you had another module called `tinyfile`, `__name__` would be "tinyfile" inside that module.

All code in Python is in a module, even code you type directly into the Python shell. Let's see this in action.

```
❯ python3
Python 3.7.7 (default, Mar 10 2020, 15:43:33)
[Clang 11.0.0 (clang-1100.0.33.17)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> __name__
'__main__'
>>> import random
>>> def roll_dice(diceexp):
...     """
...     Given a dice expression like '2d8', roll that many dice of
...     the requested size, calculate the total, and return that total.
...     """
...
...     numstr, sizestr = diceexp.split("d")
...     num = int(numstr)
...     size = int(sizestr)
...     total = sum([random.randint(1, size) for _ in range(num)])
...     return total
...
>>> roll_dice("2d8")
10
>>> import sys
>>> sys.modules["__main__"]
<module '__main__' (built-in)>
>>> main = sys.modules["__main__"]
>>> main.roll_dice("2d8")
```

Note the first line. `__name__` is equal to `"__main__"` in the shell.

We've go on to define a function, `roll_dice`, in the Python shell. We can call this function by typing `roll_dice("2d8")`.

`sys.modules` is a dictionary of all the modules Python has loaded and available. When we get the value associated with the key `__main__`, we get a module back. What's in that module? `roll_dice` is! We defined it in the shell, so it's part of the module named `__main__`.

## How this applies to `if __name__ == "__main__"`

When you run `python3 my_program.py`, it's like running your program in the Python shell. The current module -- that is, your program -- has the name `__main__`. So, in your program, if you write code under the if statement `if __name__ == "__main__"`, that code will run when you execute it from the command line. If you import your module in another program, however, that code will not run. Why? Because at that time, your module's name is whatever it's file name is.

In the below example, I will put the following code into a file `dice.py`.

```py
# dice.py
import random

def roll_dice(diceexp):
    """
    Given a dice expression like '2d8', roll that many dice of
    the requested size, calculate the total, and return that total.
    """

    numstr, sizestr = diceexp.split("d")
    num = int(numstr)
    size = int(sizestr)
    total = sum([random.randint(1, size) for _ in range(num)])
    return total

print("__name__ =", __name__)

if __name__ == "__main__":
    print("roll 2d8", roll_dice("2d8"))
```

Notice particularly that I have the line `print(__name__)`. No matter how we use this file, that line will be called.

```
❯ python3 dice.py
__name__ = __main__ # <------ look at this
roll 2d8 6

❯ python3
Python 3.7.7 (default, Mar 10 2020, 15:43:33)
[Clang 11.0.0 (clang-1100.0.33.17)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import dice
__name__ = dice     # <------ look at this
>>> dice.roll_dice("2d8")
15
```

I put two comments in there to point out what to look at. In the first example, where we run dice.py directly, it lets us know that `__name__` equals "__main__" and the code under `if __name__ == "__main__"` run. In the second, we start the Python shell and import dice. It lets us know that `__name__` is equal to "dice", and the code under `if __name__ == "__main__"` does not run.

*In conclusion*, the code under the if statement `if __name__ == "__main__"` will only run when the Python file is run directly from the command line.
