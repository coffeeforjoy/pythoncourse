---
layout: post
title: "Lesson 03 Classes and Modules"
author: Jeremy
tags:
 - Applied Statistics stream
comments: true
---
## Lesson 03 - Classes and Modules

Welcome to lesson 03 or 4 - You are nearly Python programmers, from which point
we can jump into the data science and stats extensions to Python.

Again, please download this notebook from :
[here](/pythoncourse/assets/notebooks/applied/lesson 03.ipynb).

Today we will cover object orientated programming, the way we represent
everything in Python. Then we will finish with modules, how to import and write
extensions to base Python. After this we are more or less done with base Python
- you are ready to jump into the data science stack.

### Object Orientated Programming

OOP is a paradigm in programming. Python is OO, SQL is declarative, R is
functional.

When we use OOP, we talk about Classes which are types of things and Objects,
which are instances of classes, rather than being limited to built in data
types.

A helpful metaphor is to think of Pac-Man: The Ghosts all belong to the Ghost
class, whereas each individual ghost (inky, blinky, pinky and clyde) are objects
- instances of the ghost type, with their own behaviour. They can vary in their
attributes (colour, speed, eatability, behaviour...). OOP allows us to take a
more 'object orientated' approach - we can talk about the ghosts behaving in an
easy to understand manner!

By it's nature, OOP doesnt play nicely with tabular databases. Depending on our
classes and implementations, SQL is not a good match - we can use noSQL or ODB,
or use a more tabular friendly class.

So far, everything we have seen in Python is a Class (lists, strings, dicts, etc
etc.). One of the great flexibilities in Python is an ability to make our own
Classes.

### Classes

Recall we have methods, which are specific to certain data types:

**In [1]:**

{% highlight python %}
l = [1,2,3]
print(type(l))
print(l.pop())
s = 'abc'
print(type(s))
print(s.pop())
{% endhighlight %}

    <class 'list'>
    3
    <class 'str'>



    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-1-ada37cb1e2ab> in <module>()
          4 s = 'abc'
          5 print(type(s))
    ----> 6 print(s.pop())


    AttributeError: 'str' object has no attribute 'pop'


How is this implemented? Python has a Class called list, and a Class called
string. Inside these classes, we have built in functions that only work on
objects of that class.

Let's start by defining our own (basic and for now empty) class, Customer
(convention says to use a capital):

**In [6]:**

{% highlight python %}
class Customer:
    pass
{% endhighlight %}

**In [7]:**

{% highlight python %}
x = Customer()
print(type(x))
{% endhighlight %}

    <class '__main__.Customer'>


We will talk about the \_\_main\_\_ part in the second half of the lesson, but
for now we can see we just made a new data type! Technically, we made a new
class, Customer, and a new instance, x.

How can we make it useful? We can add attributes. Attributes can take any value,
we can imagine them as 'slots' available in an object:

**In [1]:**

{% highlight python %}
class Customer: #no need for (object) like in python 2
    '''
    classes have docstrings too!
    '''
    type = 'buyer' #don't use a list!

x = Customer()
help(x)
print(x.type)
#we can add outside, if we really want:
x.foo = 'spam'
print(x.foo)
print(x)
{% endhighlight %}

    Help on Customer in module __main__ object:

    class Customer(builtins.object)
     |  classes have docstrings too!
     |  
     |  Data descriptors defined here:
     |  
     |  __dict__
     |      dictionary for instance variables (if defined)
     |  
     |  __weakref__
     |      list of weak references to the object (if defined)
     |  
     |  ----------------------------------------------------------------------
     |  Data and other attributes defined here:
     |  
     |  type = 'buyer'

    buyer
    spam
    <__main__.Customer object at 0x7f61c185ca58>


Once we have an attribute, we can access it using x.attribute.

However, a hard coded attribute is not super useful: So we can use functions as
attributes (to make methods).
This explains why we need to use the () on methods - they are functions, but not
on other attributes.

The most important method, is a special method, called \_\_init\_\_:

**In [9]:**

{% highlight python %}
class Customer:
    type = 'buyer'
    def __init__(self, customerid, status):
        self.custid = customerid
        self.status = status

x = Customer('ACME co.', 'bankrupt')
x.custid
{% endhighlight %}




    'ACME co.'



\_\_init\_\_ is the function called when we create a new object of our class.

We can add on as many attributes as we like, and use \*, \*\*, default values as
required. This works just like other functions (the scope is local to the object
- the class works like an enclosing function).

Functions always have the object itself as the first argument. It is convention
to write 'self' first, but Python will use whatever you put first - so be
careful to include it.

**In [10]:**

{% highlight python %}
class Customer:
    type = 'buyer'
    def __init__(self, customerid, status, address = None):
        self.custid = customerid
        self.status = status
        self.transactions = {}
        self.address = address

    def addtrans(self, transaction):
        self.transactions.update(transaction)

x = Customer('ACME co.', 'current')
x.addtrans({1: ["toothpaste", "bananas", "oranges"]})
print(x.transactions)
print(x.transactions[1])
{% endhighlight %}

    {1: ['toothpaste', 'bananas', 'oranges']}
    ['toothpaste', 'bananas', 'oranges']


We can use Classes inside other classes.

Let's create a basket class:

**In [11]:**

{% highlight python %}
class basket:
    def __init__(self, items, quantities, costs):
        self.items = items
        self.quantities = quantities
        self.costs = costs
    def basketprice(self):
        return round(sum([x*y for x,y in zip(self.quantities, self.costs)]),2)

x = basket(["toothpaste", "bananas", "oranges"], [3,4,5], [1.5, 1.99, 3.49])
x.basketprice()
{% endhighlight %}




    29.91



Now we can add this into our Customer objects:

**In [12]:**

{% highlight python %}
x = Customer('ACME co.', 'current')
x.addtrans({1: basket(["toothpaste", "bananas", "oranges"], [3,4,5], [1.5, 1.99, 3.49])})
print(x.transactions)
print(x.transactions[1].basketprice())
{% endhighlight %}

    {1: <__main__.basket object at 0x7f03981975c0>}
    29.91


We can continue on adding and adding to our classes. Here is one last function -
what is it doing?

**In [13]:**

{% highlight python %}
class Customer:
    type = 'buyer'
    def __init__(self, customerid, status, address = None):
        self.custid = customerid
        self.status = status
        self.transactions = {}
        self.address = address

    def addtrans(self, transaction):
        self.transactions.update(transaction)

    def mysteryfunction(self):
        return sum([y.basketprice() for x,y in self.transactions.items()])

x = Customer('ACME co.', 'current')
x.addtrans({1: basket(["toothpaste", "bananas", "oranges"], [3,4,5], [1.5, 1.99, 3.49])})
x.addtrans({2: basket(['apples', 'peanut butter'], [6,2], [3.27, 7.99])})
x.mysteryfunction()
{% endhighlight %}




    65.51



### Special Methods

There are a number of special methods we can use, as they are commonly used
functions which we can run differently based on how we define them (R users may
take note of how plot(lm(x~y)), or just print(lm(x~y)) work). Here are some of
the more useful ones - you can find the [whole list
here](https://docs.python.org/3/reference/datamodel.html).

**In [14]:**

{% highlight python %}
class Customer:
    type = 'buyer'
    def __init__(self, customerid, status, address = None, transactions = None):
        self.custid = customerid
        self.status = status
        self.transactions = {}
        if transactions is not None:
            self.addtrans(transactions) #can call methods within init
        self.address = address

    def addtrans(self, transaction):
        self.transactions.update(transaction)

    def mysteryfunction(self):
        return sum([y.basketprice() for x,y in self.transactions.items()])

    def __str__(self):
        return("Customer: {x} Status: {y} Total Transactions: {z}".format(x = self.custid,
                                                                          y = self.status,
                                                                          z = len(self.transactions)))
    def __len__(self):
        return(len(self.transactions))

    def __del__(self):
        print("You just deleted Customer: {x}".format(x = self.custid))

x = Customer('ACME co.', 'current', transactions = {3:basket(["dynamite"],[1],[99.95])})
x.addtrans({1: basket(["toothpaste", "bananas", "oranges"], [3,4,5], [1.5, 1.99, 3.49])})
x.addtrans({2: basket(['apples', 'peanut butter'], [6,2], [3.27, 7.99])})
print(x)
print(len(x))
del(x)
{% endhighlight %}

    Customer: ACME co. Status: current Total Transactions: 3
    3
    You just deleted Customer: ACME co.


### Inheritance

We can create subclasses, which inherit from their parents.

In this case, imagine we want a new class, Restuarants, which are similar to
other clients, but have additional data we want to store:

**In [15]:**

{% highlight python %}
class Restuarant(Customer):
    type = 'Restuarant'
    def __init__(self, customerid, status, tags, address = None):
        self.custid = customerid
        self.status = status
        self.transactions = {}
        self.address = address
        self.tags = tags

x = Restuarant("Canoe", "open", ["fancy", "gourmet", "canadian"])
x.addtrans({2: basket(['salmon roe', 'elder flowers'], [6,2], [3.27, 7.99])})
print(x)
print(x.type)
print(x.tags)
{% endhighlight %}

    Customer: Canoe Status: open Total Transactions: 1
    Restuarant
    ['fancy', 'gourmet', 'canadian']


We can check what type x is, but also see how isinstance is a little different:

**In [16]:**

{% highlight python %}
print(type(x))
print(isinstance(x, Restuarant))
print(isinstance(x, Customer))
{% endhighlight %}

    <class '__main__.Restuarant'>
    True
    True


Using this behaviour, we can see some interesting stuff:

**In [17]:**

{% highlight python %}
print(isinstance(True, int))
{% endhighlight %}

    True


Huh! Turns out, when Booleans were introduced into Python, they copied the C89
implementation, which simply used 1 for True, and 0 for False. So that code
didn't break, they implemented booleans as a subclass of integers and never
changed it, to avoid broken code. Maybe now int(True) and int(False) make more
sense.


Why would we want to use objects? For most day to day uses, we might not....

But, some of the most useful Python extensions make extensive use of new object
types.

The most useful for us will be DataFrames in Pandas, but almost every useful
Python package comes with classes. It provides a level of abstraction that is
hard to beat with lists, dicts tuples and sets.


## Modules

Which moves us nicely into modules. As mentioned throughout the course, Python
was built with extensibility in mind, and thus people and companies have
extended it.

Popular Python modules have been developed for almost anything you can think of.
[PyPI](https://pypi.python.org/pypi) hold over 70,000 packages in their database
and there are many more available on github and other websites. They range from
webscraping (eg beautiful soup), web development (eg django, flask), game
development (eg pygame), to our focus, data science.

We are using Anaconda, which comes with the most popular data science modules
built in, so we will cover module loading first, before moving on to
installation and development.

### Importing Modules

Say we want to have Python generate a random number from a normal distribution.
We could write our own function, but this is almost certainly already written by
someone.

We can google for a method - and we quickly find [this
page](https://docs.python.org/3.5/library/random.html). It is the description of
a whole library (aka module aka package) devoted to random number generation.
Great! But how can we use the functions?

**In [19]:**

{% highlight python %}
random.normalvariate(1, 1)
{% endhighlight %}


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-19-341ba4b07659> in <module>()
    ----> 1 random.normalvariate(1, 1)


    NameError: name 'random' is not defined


We have to first import the library!

We can do this in multiple ways:

**In [20]:**

{% highlight python %}
import random #we can import the whole random package
random.normalvariate(1, 1) # we have to use random.function to call functions
{% endhighlight %}




    0.1952982613761589



**In [21]:**

{% highlight python %}
import random as rd # we can rename the package on importation so we dont have to type it
rd.normalvariate(1, 1) # we have to use rd.function to call functions
{% endhighlight %}




    0.4454923168029481



**In [22]:**

{% highlight python %}
from random import normalvariate # we can import only one function
normalvariate(1,1) #now we just use the function
{% endhighlight %}




    0.7398876099155046



**In [23]:**

{% highlight python %}
from random import * #if the package supports it, we can import everythin (not recommended!)
normalvariate(1,1) #now we can use any function
{% endhighlight %}




    -0.07930331195414042



We can import multiple packages and functions at once using tuples (without
brackets too):

**In [24]:**

{% highlight python %}
from random import normalvariate, gauss
{% endhighlight %}

**In [25]:**

{% highlight python %}
import random, sys
{% endhighlight %}

The exact imports depend on your need. If you can, only import one or two
functions to keep the namespace clean. Some packages have their own idiomatic
import names - pandas is almost always import as pd, seaborn as sns. You will
pick these up by reading other peoples code online.

By convention, modules are imported at the top of a script or notebook - this
helps to let people know what the dependencies are.

### Installing Packages

Sometimes the package you would like does not come installed on the system. In
this case you can install it either using conda, or pip.

As we are using Anaconda, we will use the conda installer first, and pip as a
backup. Both are equivalent, the conda version will work a little nicer with our
existing set up. Briefly, conda includes a virtual env, while pip does not.

Conda and pip work at the command line, not in Python. Linux users will be
familiar with yum or apt-get - these are similar repositories. R users will know
CRAN - again a similar idea, but CRAN is much more picky about what can be
hosted. Think of pip and PyPI as an uncurated CRAN - install packages at your
own risk.

Conda syntax is:

`conda update conda` to update conda (or other packages)

`conda install seaborn` to install (in this case seaborn)

At which point you will be asked some questions, and prompted to install or
upgrade.

pip stands for pip installs packages, and has become the default Python
module/library/package repo.

Pip syntax is:

`pip install --upgrade pip` to update pip (or other packages)

`pip install seaborn` to install


### Making our own modules

Making our Python scripts into modules is the easiest way to have scripts work
with each other. Let's write a script and save it as example.py:

**In [26]:**

{% highlight python %}
import sys

def myfun(a,b):
    return(a + b)

a = myfun(sys.argv[1], sys.argv[2])
print(a)
{% endhighlight %}

    -f/run/user/1000/jupyter/kernel-393275c7-f9da-4664-9b41-58323359f987.json


We can save it in a lot of locations - Python looks in the directory we are
currently in, and the system path for python. We can find the system path, using
the system module, sys. We search in order from the first dir to the last.

**In [27]:**

{% highlight python %}
import sys
print(sys.path)
{% endhighlight %}

    ['', '/home/jeremy/anaconda3/lib/python35.zip', '/home/jeremy/anaconda3/lib/python3.5', '/home/jeremy/anaconda3/lib/python3.5/plat-linux', '/home/jeremy/anaconda3/lib/python3.5/lib-dynload', '/home/jeremy/anaconda3/lib/python3.5/site-packages/Sphinx-1.3.1-py3.5.egg', '/home/jeremy/anaconda3/lib/python3.5/site-packages/setuptools-18.5-py3.5.egg', '/home/jeremy/anaconda3/lib/python3.5/site-packages', '/home/jeremy/anaconda3/lib/python3.5/site-packages/cryptography-1.0.2-py3.5-linux-x86_64.egg', '/home/jeremy/anaconda3/lib/python3.5/site-packages/IPython/extensions', '/home/jeremy/.ipython']


we can run from the command line, or use %run to run our script:

**In [28]:**

{% highlight python %}
%run '/home/jeremy/.ipython/example.py' 1 2
{% endhighlight %}

    12


Ouch - everything gets passed in as strings from sys.argv. Can you remember how
to fix this?

What if we don't want to run it, but to have access to the function?

We can import!

**In [29]:**

{% highlight python %}
import example
{% endhighlight %}

    -f/run/user/1000/jupyter/kernel-393275c7-f9da-4664-9b41-58323359f987.json


**In [30]:**

{% highlight python %}
print(example.myfun(1,2))
print(example.a)
{% endhighlight %}

    3
    -f/run/user/1000/jupyter/kernel-393275c7-f9da-4664-9b41-58323359f987.json


We can see that the code in our function was executed when we imported, which we
might not want.

To fix this we can use a couple of tricks.

When we work in Python, the global environment is stored as a massive dict, with
items as the values, and their names as keys:

**In [31]:**

{% highlight python %}
print(globals().keys())
{% endhighlight %}

    dict_keys(['_i19', '_i22', '_i15', 'expovariate', 'total_ordering', '_i', 'basket', '__builtins__', 'y', 'shuffle', 'gammavariate', 'triangular', 'SystemRandom', 'random', '_i11', '_i10', '_i20', 'randrange', '__doc__', '_i21', '_i16', '__builtin__', '_22', 'getstate', '_i14', '__package__', 'betavariate', '_', '_sh', 'Random', '_i25', 'setstate', 'x', '_i5', 'seed', 'get_ipython', 'rd', 'l', 'Mystr', '_i6', '_i27', '_i1', '_20', '_iii', 'paretovariate', 'example', 'sample', 'exit', 'Out', '_i7', '_dh', 'myfun', '_i26', '_i28', 'In', '_9', '_ii', '___', 's', '_i8', 'choice', '_i2', '_23', 'weibullvariate', '_ih', '_i18', '_11', 'uniform', '_i30', '_i12', 'quit', '__spec__', 'normalvariate', '_i3', 'randint', 'Customer', '_13', 'sys', 'getrandbits', 'gauss', '_i23', '_i17', '__loader__', '_oh', '_i24', 'lognormvariate', '_i9', '__name__', 'Restuarant', '__nonzero__', '_i31', '_i29', '_i4', 'a', '_21', '_i13', 'vonmisesvariate', '__'])


You can see random, and example in there. Where is our main script?

It is called \_\_main\_\_. Using this knowledge, we can edit our script, to only
run if name is \_\_main\_\_:

**In [32]:**

{% highlight python %}
import sys

def myfun(a,b):
    return(a + b)

if __name__ == "__main__":
    a = myfun(sys.argv[1], sys.argv[2])
    print(a)
{% endhighlight %}

    -f/run/user/1000/jupyter/kernel-393275c7-f9da-4664-9b41-58323359f987.json


**In [33]:**

{% highlight python %}
%run '/home/jeremy/.ipython/example2.py' 1 2
{% endhighlight %}

    12


But importing no longer causes execution:

**In [34]:**

{% highlight python %}
import example2
{% endhighlight %}

More on script layout, module organisation and scoping in lesson 7, but for now,
know that checking for name is the standard way of creating modules which can
both be run, and imported.

## Summary

You now know basic python data types, statements, functions, classes and
modules. We will now move onto the application of this knowledge to data
science. If you'd like, this is a great stepping stone to start learning Python
web development, game development, app development etc etc too. BTW the [initial
hello world for flask](http://flask.pocoo.org/) uses a decorator!

We now have to test you! The test is a formative assessment - I'm more interested
in how well you are learning than giving a grade. To these ends, please treat it
as open book (and open internet). Google, stackoverflow, and the official Python
docs are all useful and usable. Please though, do not give answers to your
colleagues - we want to know where everyone is up to.

## Exercises

* Create a class, Circle, that has a radius attribute given to it in
\_\_init\_\_

* Modify this class to have an attribute, pi = 3.14

* Add methods to Circle to calculate diameter, area and circumference

* Find a Python module which has pi, import it and use pi instead of 3.14

* Create a new class, Triangle, which has 3 lines lengths, atob, btoc and ctoa

* Create a subclass that inherits from triangle, RightTriangle, that uses
assert to ensure the triangle is right angled (you might need to use google
to figure out the test)

* Give RightTriangle methods for finding the angles at a, b and c. You will need
to import an external library with trig functions.

* Add a method to calculate the area of the RightTriangle