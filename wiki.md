# object-oriented-SLMS
COM5012 Object Oriented Programming Assignment Record:

1. Requirements: 

The Smart Library Management System will fulfil a set of functional and non-functional requirements.
System Functions:
- allow members to log in to a secure account to view personal information on books borrowed.
- display a list of books available.
- allow members to search for books by author/title.
- allow members to borrow up to 5 books at a time, and return them.
- allow members reserve books that are currently borrowed.
- track a book's return by date, and possibly issue an overdue warning to members.
functions for librarians:
- access book records within the system, adding, updating, and removing books
- handle borrow and reserve requests from members
- create simple reports such as a collection of overdue books


Non-functions of the system:
- user interface should be accessible and simple, a basic menu-driven one.
- the program should be easy to maintain and debug.
- the program should be efficient and make good use of memory to react quickly to input.
- object oriented approach used:
    - inheritance simplifies the workflow by avoiding repitition of shared properties beneath base classes.
    - encapsulation hides the internal data, maintaining privacy whilst allowing it to be used through methods.
    - polymorphism allows a shared UI to display item types in different ways, depending on who is accessing data.
