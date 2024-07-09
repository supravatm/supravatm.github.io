---
layout: default
title:  "Abstract Class and interface"
date:   2018-08-22 60:03:25 -0800
tags: [github, Magento 2, supravatm, blog, cms, opensource, wordpress, blogger]
categories: [magento2]
image: magento2.png
---

**Abstract Class**:
* An abstract class is a class that contains at least one abstract method. The abstract method is function declaration without anybody and it has the only name of the method and its parameters.
* There can be any number of methods in the class and we have to declare the class as abstract only when there is an abstract method.

<p>An <b>abstract</b> class is a class that is only partially implemented by the programmer. It may contain one or more abstract methods. An abstract method is simply a function definition that serves to tell the programmer that the method must be implemented in a child class.</p>

**Notes on Abstract class:**

* Objects cannot be created for the abstract classes.
* If a class has only one method as abstract, then that class must be an abstract class.
*The child class which extends an abstract class must define all the methods of the abstract class.
* If the abstract method is defined as protected in the parent class, the function implementation must be defined as either protected or public, but not private.
* The signatures of the methods must match, optional parameter given in the child class will not be accepted and error will be shown.
>Abstract classes that declare all their methods as abstract are not interfaces with different names. One can implement multiple interfaces, but not extend multiple classes (or abstract classes).

**interface**:
<p>An <b><i>interface</i></b> is similar to an abstract class; indeed interfaces occupy the same namespace as classes and abstract classes. For that reason, you cannot define an interface with the same name as a class. An interface is a fully abstract class; none of its methods are implemented and instead of a class subclassing from it, it is said to implement that interface.</p>

**Rules of Interfaces:**
* All methods declared in an interface must be public; this is the nature of an interface.
* All methods in the interface must be implemented within a class; failure to do so will result in a fatal error.
* The class implementing the interface must use the exact same method signatures as are defined in the interface
* Interfaces can be extended like classes using the extends operator.

**Note on Interfaces:**
* We cannot create objects to interface, but the class implementing the interface can have objects.
* We cannot define a variable in an interface.
* If we extend interface all the methods of the interface must be implemented in the child class.


| Abstract class        | Interface           |
| ------------- |:-------------:|
| It can have constants, members, method stubs (methods without a body), methods | It can only have constants and methods stubs.|
| Methods and members can have public or protected visibility      | Methods of interface should only be public not any other visibility      |
| The concept of multiple inheritances not supported | An interface can extend or a class can implement multiple other interfaces.      |
| Child class must implement all the abstract method of parent class when extend keyword is used. | No need of implementing methods from parent interface when interface is extending another interface      |
