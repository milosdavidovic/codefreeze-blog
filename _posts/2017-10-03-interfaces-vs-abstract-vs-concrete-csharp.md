---
title: 'Concrete vs Abstract vs Interface'
layout: single
published: false
---
## Concrete vs Abstract vs Interface

## Content

[Concrete Classes](#concrete-classes)

[Abstract Classes](#abstract-classes)

[Interfaces](#interfaces)

### Concrete Classes
With inheritance in mind, concrete classes have several disadvantages and gotchas that make them less appealing candidates to derive from. If, for example, the method is not abstract, nothing forces child class to implement it. If this is unintentional, it could lead to the runtime error.

### Abstract Classes
Abstract classes on the other side contain abstract members, forcing child classes to implement these members, thus enforcing functionalities. Also, we can make use of access modifiers: public, private, protected and internal, to fine tune what we want to expose.
Abstract classes can contain everything we can put in the normal class (fields, properties, constructors, destructors, methods, events, and indexers).
The main limitation with class inheritance is that it is only possible to derive from a single class. 

### Interfaces
Interfaces are best described as contracts, the list of members we expect to be implemented by some class. The class implementing the interface are obligated to implement all its members. All these members of the class are automatically public.
You can implement multiple interfaces (aggregation)
All members are automatically public (contract terms)
Can contain only Properties, Methods, Events and Indexers
