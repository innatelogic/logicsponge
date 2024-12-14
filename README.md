<img src="media/logicsponge.png" alt="LogicSponge Logo" width="350">

logicsponge is a Python framework designed for computing with data streams, including monitoring, modeling, and predicting real-time data.

# Key features

- A Python-integrated language
- Easy-to-build custom pipelines, reuse of components
- Live dashboards
- Upcoming: Simple to deploy without the need to setup extra infrastructure, frameworks, or databases

# Code base
Check out our repositories:

- The main codebase, containing the core infrastructure for building streaming data pipelines:  
https://github.com/innatelogic/logicsponge-core/  
  
- Tools for process mining and event-log analysis and prediction:  
https://github.com/innatelogic/logicsponge-processmining

- Monitoring Real-Time Patterns in Streaming Data:  
https://github.com/innatelogic/logicsponge-monitoring  
  
- A collection of example pipelines to help you get started:  
https://github.com/innatelogic/logicsponge-examples  

# Getting started

To get started with **logicsponge**, you can simply install it using **pip**:

```sh
pip install logicsponge
```


# Hello World!
We'll start with a must-have Hello World!

```python
# hello_world.py

import time

import logicsponge.core as ls


# Define the Hello term
class Hello(ls.SourceTerm):
    def run(self):
        incomplete_message = "Hello"
        out = ls.DataItem({"message": incomplete_message})
        self.output(out)
        time.sleep(1)


# Define the World term
class World(ls.FunctionTerm):
    def f(self, item: ls.DataItem) -> ls.DataItem:
        incomplete_message = item["message"]
        complete_message = incomplete_message + " World!"
        out = {"message": complete_message}
        return ls.DataItem(out)

    
# Build and start the sponge
sponge = (
    Hello()
    * World()
    * ls.Print()
)

sponge.start()
```

Let's break it down. We define a sequential **data pipeline** that has three **terms** (you can think of a term as a **node** or **gate**):

1. A custom **source** **`Hello`**, which produces a stream of **data items**, each containing an incomplete message `"Hello"`.
2. A custom **term** **`World`**, which receives these data items, completes the messages contained in them to `"Hello World!"`, and emits them as new **data items**.
3. A **term** **`Print`** that prints the incoming **data item**.

A closer look into `Hello` and `World` will already make you familiar with two main concepts of logicsponge:

### Source term ```Hello```

`Hello` overwrites the `run` method of its superclass `SourceTerm`, which can be used for terms
that do not expect any inputs:
```python
class Hello(ls.SourceTerm):
    def run(self):
        [...]
``` 
It is equipped with an implicit while loop, i.e.,
its body is executed over and over again (under the hood, it is in the scope of `while True`). This is ideal for
source terms, but can also prove useful when several input streams need to be orchestrated in complex ways. We will come
back to the endless possibilities in later tutorials. So, `Hello` simply creates an infinite stream of
**data items**. A data item is more or less a standard Python dictionary, though, internally, it is equipped with
additional information such as a timestamp. Note that, as it possibly runs forever, ```run``` does not have a return
statement. However, using ```self.output```, you can add data items to the output stream at your discretion,
allowing them to be processed by subsequent terms.

### Function term ```World```

`World` overwrites the `f` method of its superclass `FunctionTerm`:
```python
class World(ls.FunctionTerm):
    def f(self, item: ls.DataItem) -> ls.DataItem:
        [...]
``` 
The ```f``` method is very convenient for any simple consumer producer process. Put simply, it takes a data item
and then returns a data item. That's it! In our example, we simply append ``` World!``` (with a leading space)
to the message before outputting it.

### 3, 2, 1, ... Start!

It just remains to arrange the two building blocks, together with the built-in ```Print``` term,
in a **sponge** (well, in this case a simple pipeline), and here we go:

```python
sponge = (
    Hello()
    * World()
    * ls.Print()
)

sponge.start()
```

Now you can go ahead and execute ```hello_world.py```:

```shell
python hello_world.py
```

We'll be back soon with more tutorials to help you become a logicsponge pro. Stay tuned!
