---
layout: post
title: "Data Containers in Python"
date: 2022-09-18 18:38:46 +0300
category: programming
tags: 
  - python3 python
---

How often did you find yourself implementing loads of objects or classes which only purpose was to keep data for a moment on its way to somewhere else? For me, it happens all the time especially if I'm sticking to [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) or similar principles of code organization. The principles talk about isolation of your packages and managment of directions of their dependencies. In practice, this usually means that separate packages implement similar aspects of the domain by the code structures which are close but not the same. For example, the same User concept will have slightly different implementation in your domain model, database, API and Identity Provider. And voila, you have multiple User data containers in different parts of your system which you should manipulate in your Python code, preferably, the most efficient way.
The [Python Data Model](https://docs.python.org/3/reference/datamodel.html) and [Python Standard Library](https://docs.python.org/3/library/) provides multiple options to solve the problem. But before let's agree on what "data container" is.

## What Is a Data Container?
Data container is an object that holds only state and no behavior. In Object-Oriented Programming (OOP), it means an object or a class with data fields and no methods. In the code, the data objects are commonly used as data transfer objects (DTO), storage of a database records or API request or response, or concepts of your domain. The latter, if overused, can result in [Anemic Domain Model](https://martinfowler.com/bliki/AnemicDomainModel.html) which you should avoid most of the time.

## Implementation Options
So, which options to model data containers does Python have out of the box? Here they are:
* class
* namedtuple
* NamedTuple
* dataclass

This isn't by far an exhaustive list of built-in options. But I find them common in my everyday job. So, let's briefly consider them one by one.

### class
The official "The Python Tutorial" says: "Classes provide a means of bundling data and functionality together". The data bundling feature makes class a good candidate on the Data Container role. In statically compiled languages like C# or Java, "class" is the most commonly used feature to implement data containers. Implementation of a typical User class:

```Python
class User:
  def __init__(self, name: str, email: str, age: int, role: str):
    self.name = name
    self.email = email
    self.age = age
    self.role = role
```

No methods (or behavior) there, just fields (or state).

### namedtuple


### NamedTuple

### dataclass

## Conclusion