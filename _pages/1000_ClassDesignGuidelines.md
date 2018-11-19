---
title: Class Design Guidelines
permalink: /class-design-guidelines/
classes: wide
sidebar:
  nav: "sidebar"
---

### <a name="av1000"></a> A class or interface should have a single purpose (AV1000) ![](/assets/images/1.png) ![](/assets/images/A.png)
A class or interface should have a single purpose within the system it functions in. In general, a class either represents a primitive type like an email or ISBN number, an abstraction of some business concept, a plain data structure, or is responsible for orchestrating the interaction between other classes. It is never a combination of those. This rule is widely known as the [Single Responsibility Principle](https://8thlight.com/blog/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html), one of the S.O.L.I.D. principles.
**Tip:** A class with the word `And` in it is an obvious violation of this rule.
**Tip:** Use [Design Patterns](http://en.wikipedia.org/wiki/Design_pattern_(computer_science)) to communicate the intent of a class. If you can't assign a single design pattern to a class, chances are that it is doing more than one thing.
**Note** If you create a class representing a primitive type you can greatly simplify its use by making it immutable.

### <a name="av1001"></a> Only create a constructor that returns a useful object (AV1001) ![](/assets/images/3.png)
There should be no need to set additional properties before the object can be used for whatever purpose it was designed. However, if your constructor needs more than five parameters (which violates AV1561), your class might have too much responsibility (and violates AV1000).

### <a name="av1003"></a> An interface should be small and focused (AV1003) ![](/assets/images/2.png)
Interfaces should have a name that clearly explains their purpose or role in the system. It should ideally have 3 to 5 members. Do not combine many vaguely related members on the same interface just because they were all on the same class. Separate the members based on the responsibility of those members, so that callers only need to call or implement the interface related to a particular task. This rule is more commonly known as the [Interface Segregation Principle](https://lostechies.com/wp-content/uploads/2011/03/pablos_solid_ebook.pdf).

### <a name="av1004"></a> Use an interface rather than a base class to support multiple implementations (AV1004) ![](/assets/images/3.png)
If you want to expose an extension point from your class, expose it as an interface rather than as a base class. You don't want to force users of that extension point to derive their implementations from a base class that might have an undesired behavior. However, for their convenience you may implement a(n abstract) default implementation that can serve as a starting point.

### <a name="av1005"></a> Use an interface to decouple classes from each other (AV1005) ![](/assets/images/2.png)
Interfaces are a very effective mechanism for decoupling classes from each other:

- They can prevent bidirectional associations; 
- They simplify the replacement of one implementation with another; 
- They allow the replacement of an expensive external service or resource with a temporary stub for use in a non-production environment.
- They allow the replacement of the actual implementation with a dummy implementation or a fake object in a unit test; 
- Using a dependency injection framework you can centralize the choice of which class is used whenever a specific interface is requested.

### <a name="av1011"></a> It should be possible to treat a derived object as if it were a base class object (AV1011) ![](/assets/images/2.png)
In other words, you should be able to use a reference to an object of a derived class wherever a reference to its base class object is used without knowing the specific derived class. A very notorious example of a violation of this rule is throwing a `NotImplementedException` when overriding some of the base-class methods. A less subtle example is not honoring the behavior expected by the base class.     
**Note:** This rule is also known as the Liskov Substitution Principle, one of the [S.O.L.I.D.](http://www.lostechies.com/blogs/chad_myers/archive/2008/03/07/pablo-s-topic-of-the-month-march-solid-principles.aspx) principles.

### <a name="av1013"></a> Don't refer to derived classes from the base class (AV1013) ![](/assets/images/1.png)
Having dependencies from a base class to its sub-classes goes against proper object-oriented design and might prevent other developers from adding new derived classes.

### <a name="av1014"></a> Avoid exposing the other objects an object depends on (AV1014) ![](/assets/images/2.png)
If you find yourself writing code like this then you might be violating the [Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter).

	someObject.SomeProperty.GetChild().Foo()

An object should not expose any other classes it depends on because callers may misuse that exposed property or method to access the object behind it. By doing so, you allow calling code to become coupled to the class you are using, and thereby limiting the chance that you can easily replace it in a future stage.
**Note:** Using a class that is designed using the [Fluent Interface](http://en.wikipedia.org/wiki/Fluent_interface) pattern seems to violate this rule, but it is simply returning itself so that method chaining is allowed.
**Exception:** Inversion of Control or Dependency Injection frameworks often require you to expose a dependency as a public property. As long as this property is not used for anything other than dependency injection I would not consider it a violation.

### <a name="av1020"></a> Avoid bidirectional dependencies (AV1020) ![](/assets/images/1.png)
This means that two classes know about each other's public members or rely on each other's internal behavior. Refactoring or replacing one of those classes requires changes on both parties and may involve a lot of unexpected work. The most obvious way of breaking that dependency is to introduce an interface for one of the classes and using Dependency Injection.
**Exception:** Domain models such as defined in [Domain-Driven Design](http://domaindrivendesign.org/) tend to occasionally involve bidirectional associations that model real-life associations. In those cases, make sure they are really necessary, and if they are, keep them in.

