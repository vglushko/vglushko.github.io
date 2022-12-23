---
layout: post
title: "Data Transfer Objects (DTO) in Python"
date: 2022-10-27 09:10:46 +0300
category: design
tags: 
  - python
  - development
---

How often did you find yourself implementing loads of objects or classes which only purpose were to keep data for a moment on the route from one software component to another? Usually, it happens all the time especially if you are separating the concerns in software, probably, according to [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html){:target="_blank"}, [Layered Architecture](https://en.wikipedia.org/wiki/Multitier_architecture){:target="_blank"}, or similar principles of code organization. <!--more--> According to them, you are taking care of isolation of your components and managment of directions of their dependencies. Some components communicate with others to do their job and inevitably share a part of their state. You want to avoid any leakage of the component specifics and keep their coupling as low as possible. So, you use [Data Transfer Objects](https://en.wikipedia.org/wiki/Data_transfer_object){:target="_blank"} (DTO) to shape the component communication. Commonly, from implementation perspective, DTOs are objects with the state and no behavior. The [Python Data Model](https://docs.python.org/3/reference/datamodel.html){:target="_blank"} and [Python Standard Library](https://docs.python.org/3/library/){:target="_blank"} offer several options to solve the problem. Among of them are: plain old Python [classes](https://docs.python.org/3/tutorial/classes.html){:target="_blank"}, [collections.namedtuple](https://docs.python.org/3/library/collections.html#collections.namedtuple){:target="_blank"}, [typing.NamedTuple](https://docs.python.org/3/library/typing.html#typing.NamedTuple) and [dataclasses.dataclass](https://docs.python.org/3/library/dataclasses.html).

## Classes As a Classical DTO Implementation
This is the option that pops into the mind of anyone familiar with Object-Oriented Programming (OOP). This approach is not specific to Python. You have it in your toolbox in every OOP language. Usually, this is the first you think of when coming to Python from another OOP language like Java or C#. The general idea is simple: class with a state. A typical User class as DTO, that you may want to share, say, between your authentication component and some of the service components of your domain model, can be implemented as:

```python
class User:
    def __init__(self, name: str, email: str, age: int, role: str="guest"):
        self.name = name
        self.email = email
        self.age = age
        self.role = role

user = User("John Doe", "john.doe@example.com", 35)
```

The class-based DTO can be tuned by overriding dunder methods such as [\_\_str\_\_()](https://docs.python.org/3/reference/datamodel.html?highlight=__str__#object.__str__), [\_\_repr\_\_()](https://docs.python.org/3/reference/datamodel.html?highlight=__str__#object.__repr__), etc., directly in the class body. On the other side, it has no guarantees of object immutability out of the box (which is highly desired for DTOs in the majority of cases) and it is more verbose and memory consuming than the other approaches considered below. 

## Simple collections.namedtuple() Factory Function for Immutable DTO
This is the lightweight and more Pythonic approach to DTO than the previous one. Under the hood, the colleciton.namedtuple() is implemented as the factory function that returns a new tuple subclass. If needed, you can further extend it via inheritance by overriding dunders (just watch out for [\_\_slots\_\_](https://docs.python.org/3/reference/datamodel.html#slots) = () to keep the memory consumption low in this case).
The User DTO class from the previous section can be implemented as follows:

```python
from collections import namedtuple

# Create a tuple subclass named User with the specified attributes.
User = namedtuple("User", ["name", "email", "age", "role"], defaults=["guest"])
# Create an instance of the User type.
user = User("John Doe", "john.doe@example.com", 35)
```

The tuple subclass has a useful [\_make()](https://docs.python.org/3/library/collections.html#collections.somenamedtuple._make) method that acts as another factory function. You can use it to create DTOs for records returned from a file, network stream, database, API, etc.:

```python
user = User._make(["John Doe", "john.doe@example.com", 35, "guest"]
```

The collections.namedtuple() is lighter than a regular class and has smaller memory footprint. This feature may be critical to keep the overall memory consumption low especially when lots of DTOs are massively created for data processing. As a tuple, it guarantees the immutability of the object, which is the desired DTO feature by default. Also being a tuple, it supports indexing of its fields which may come in handy to implement some tricky scenarios, like generic serialization. It also takes less amount of code to implement than a regular class, which generally improves its maintainability.
At the same time there are features that make it somewhat controversial for selection as the main approach to DTO. It accepts its 'fields' in a string format, which is poorly supported by IDE most of the time. Its extension is somewhat error-prone, as you need to remember the \_\_slots\_\_ thing.

## DTO Enhancement with typing.NamedTuple
The typing.NamedTuple is a typed version of the collections.namedtuple(). This is not a factory function anymore but a class. It inherits the interface of the collections.namedtuple() and adds the \_\_annotations\_\_ property that maps field names of the tuple to its corresponding types. You can use it as follows:

```python
from typing import NamedTuple

class User(NamedTuple):
    name: str
    email: str
    age: int
    # Fields with default values must follow other fields
    role: str = "guest"

user = User("John Doe", "john.doe@example.com", 35)
```

The typing.NamedTuple is definitely an enhancement over the untyped collections.namedtuple() factory function. It continues to support all the methods of the collections.namedtuple(), so you continue using it the way you did it before. As it is now a class, it also supports overriding the dunder methods. The IDE and tools now can assist you in solving type problems because of the supported type annotations. And you don't need to remember about that \_\_slots\_\_ thing anymore. 

## Further Improvement with dataclasses.dataclass
The dataclasses.dataclass is a decorator that can be applied to a regular Python class to automatically generate some dunder methods like \_\_init\_\_(), \_\_repr\_\_(), \_\_hash\_\_(), \_\_eq\_\_(), comparison methods, emulate immutability, etc. One of the design goals of data classes is to improve the [support of static type checkers](https://peps.python.org/pep-0557/#rationale). They also [add more robustness](https://peps.python.org/pep-0557/#why-not-just-use-namedtuple) to the behavior of some tuple methods.
A User data class with automatically generated \_\_init\_\_() and \_\_repr\_\_() methods and emulated mutability can be created as follows:

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class User:
    name: str
    email: str
    age: int
    # Fields with default values must follow other fields
    role: str = "guest"
```
The @dataclass decorator doesn't generate a new class but extends the existent one and returns it as the result. The behavior of the class fields can be further fine tuned by the [dataclasses.field()](https://docs.python.org/3/library/dataclasses.html#dataclasses.field) function. For example, you can exclude a field from the \_\_init\_\_(), \_\_repr\_\_() or \_\_hash\_\_() parameters, or provide a factory of default values, make the field a keyword-only argument, or attach a metadata.
For the sake of consistency with tuple-based approach, dataclasses module also provides the [make_dataclass()](https://docs.python.org/3/library/dataclasses.html#dataclasses.make_dataclass) function which is similar to the _make method of the namedtuple().

Data classes don't intervene with the user-defined classes and allow you to use any Python class features, like inheritance, metaclasses, user-defined methods, etc. Data classes implement type-aware comparison. This makes instances of the same content but different types not equal anymore. An IDE also gets improved support for static type checking.

## Conclusions
We looked into 3 possible options to DTO in Python. But there are more especially if you check beyond the Python Standard Library. However, those ones we considered is more than enough to cover the majority of the design and implementation cases. 
When evaluating an option that works for you the best, I would recommend to keep in mind the following advice:

| Use ...                  | If ....
| ------------------------ | ----------------- |
| Data Classes             | You plan to implement a DTO hierarchy |
|                          | Your DTOs may have rich user-defined behavior (user-defined methods) |
|                          | You would like to have more flexability in the future |
| &nbsp;                   |                                         |
| Tuples                   | Your DTOs are simple state containers with no or minimum behavior |
|                          | You don't need a hierarchy of DTO classes |
|                          | The requirements to memory is high or you create huge amount of DTOs within a short period of time |
| &nbsp;                   |                                         |
| Python classes           | You are stuck with an old Python version which doesn't support named tuples or data classes |
|                          | You have a very special case when tuples and data classes don't work |
|   |   |

From the maintainability perspective, other things being equal, prefer data classes to tuple-based approach. If you decided to go with a tuple-based approach, prefer named tuples to tuple factory function.
