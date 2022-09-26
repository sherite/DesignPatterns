ABSTRACT FACTORY - CREATIONAL DESIGN PATTERN

INTENT

Provide an interface for creating families of related or dependent objects without
specifying their concrete classes.


ALSO KNOWN AS
Kit

MOTIVATION

Consider a user interface toolkit that supports multiple look-and-feel standards,
such as Motif and Presentation Manager. Different look-and-feels define different
appearances and behaviors for user interface "widgets" like scroll bars, windows,
and buttons. To be portable across look-and-feel standards, an application should
not hard-code its widgets for a particular look and feel. Instantiating
look-and-feel-specific classes of widgets throughout the application makes it
hard to change the look and feel later.
We can solve this problem by defining an abstract WidgetFactory class that declares
an interface for creating each basic kind of widget. There's also an abstract
class for each kind of widget, and concrete subclasses implement widgets for
specific look-and-feel standards. WidgetFactory's interface has an operation that
returns a new widget object for each abstract widget class. Clients call these
operations to obtain widget instances, but clients aren't aware of the concrete
classes they're using. Thus clients stay independent of the prevailing look and
feel.

There is a concrete subclass of WidgetFactory for each look-and-feel standard.
Each subclass implements the operations to create the appropriate widget for the
look and feel. For example, the CreateScrollBar operation on the
MotifWidgetFactory instantiates and returns a Motif scroll bar, while the
corresponding operation on the PMWidgetFactory returns a scroll bar for
Presentation Manager. Clients create widgets solely through the WidgetFactory
interface and have no knowledge of the classes that implement widgets for a
particular look and feel. In other words, clients only have to commit to an interface
defined by an abstract class, not a particular concrete class.
A WidgetFactory also enforces dependencies between the concrete widget classes.
A Motif scroll bar should be used with a Motif button and a Motif text editor,
and that constraint is enforced automatically as a consequence of using a
MotifWidgetFactory.

APPLICABILITY

Use the Abstract Factory pattern when
· a system should be independent of how its products are created, composed,
and represented.
· a system should be configured with one of multiple families of products.
· a family of related product objects is designed to be used together, and
you need to enforce this constraint.
· you want to provide a class library of products, and you want to reveal
just their interfaces, not their implementations.

STRUCTURE

PARTICPANTS

· AbstractFactory (WidgetFactory)
o declares an interface for operations that create abstract product objects.

· ConcreteFactory (MotifWidgetFactory, PMWidgetFactory)
o implements the operations to create concrete product objects.

· AbstractProduct (Window, ScrollBar)
o declares an interface for a type of product object.

· ConcreteProduct (MotifWindow, MotifScrollBar)
o defines a product object to be created by the corresponding concrete factory.
o implements the AbstractProduct interface.

· Client
o uses only interfaces declared by AbstractFactory and AbstractProduct classes.

COLLABORATIONS

· Normally a single instance of a ConcreteFactory class is created at run-time.
This concrete factory creates product objects having a particular
implementation. To create different product objects, clients should use
a different concrete factory.
· AbstractFactory defers creation of product objects to its ConcreteFactory
subclass.
Design Patterns: Elements of Reusable Object-Oriented Software
102

CONSEQUENCES

The Abstract Factory pattern has the following benefits and liabilities:

1. It isolates concrete classes. The Abstract Factory pattern helps you control
the classes of objects that an application creates. Because a factory
encapsulates the responsibility and the process of creating product objects,
it isolates clients from implementation classes. Clients manipulate
instances through their abstract interfaces. Product class names are
isolated in the implementation of the concrete factory; they do not appear
in client code.

2. It makes exchanging product families easy. The class of a concrete factory
appears only once in an application—that is, where it's instantiated. This
makes it easy to change the concrete factory an application uses. It can
use different product configurations simply by changing the concrete
factory. Because an abstract factory creates a complete family of products,
the whole product family changes at once. In our user interface example,
we can switch from Motif widgets to Presentation Manager widgets simply
by switching the corresponding factory objects and recreating the
interface.

3. It promotes consistency among products. When product objects in a family
are designed to work together, it's important that an application use
objects from only one family at a time. AbstractFactory makes this easy
to enforce.

4. Supporting new kinds of products is difficult. Extending abstract factories
to produce new kinds of Products isn't easy. That's because the
AbstractFactory interface fixes the set of products that can be created.
Supporting new kinds of products requires extending the factory interface,
which involves changing the AbstractFactory class and all of its subclasses.
We discuss one solution to this problem in the Implementation section.

IMPLEMENTATION

Here are some useful techniques for implementing the Abstract Factory pattern.

1. Factories as singletons. 
An application typically needs only one instance of a ConcreteFactory 
per product family. So it's usually best implemented as a Singleton.

2. Creating the products. 
AbstractFactory only declares an interface for creating products. 
It's up to ConcreteProduct subclasses to actually create them. 
The most common way to do this is to define a factory method for each product. 
A concrete factory will specify its products by overriding the factory method
for each. While this implementation is simple, it requires a new concrete 
factory subclass for each product family, even if the product families differ
only slightly.
If many product families are possible, the concrete factory can be
implemented using the Prototype pattern. The concrete factory is
initialized with a prototypical instance of each product in the family,
and it creates a new product by cloning its prototype. The Prototype-based
approach eliminates the need for a new concrete factory class for each new
product family.
Here's a way to implement a Prototype-based factory in Smalltalk. The
concrete factory stores the prototypes to be cloned in a dictionary called
partCatalog. The method make: retrieves the prototype and clones it:
make: partName
^ (partCatalog at: partName) copy
The concrete factory has a method for adding parts to the catalog.
addPart: partTemplate named: partName
partCatalog at: partName put: partTemplate
Prototypes are added to the factory by identifying them with a symbol:
aFactory addPart: aPrototype named: #ACMEWidget
A variation on the Prototype-based approach is possible in languages that
treat classes as first-class objects (Smalltalk and Objective C, for
example). You can think of a class in these languages as a degenerate factory
that creates only one kind of product. You can store classes inside a
concrete factory that create the various concrete products in variables,
much like prototypes. These classes create new instances on behalf of the
concrete factory. You define a new factory by initializing an instance of
a concrete factory with classes of products rather than by subclassing.
This approach takes advantage of language characteristics, whereas the pure
Prototype-based approach is language-independent.
Like the Prototype-based factory in Smalltalk just discussed, the
class-based version will have a single instance variable partCatalog, which
is a dictionary whose key is the name of the part. Instead of storing
prototypes to be cloned, partCatalog stores the classes of the products.
The method make: now looks like this:
make: partName
Design Patterns: Elements of Reusable Object-Oriented Software
104
^ (partCatalog at: partName) new

3. Defining extensible factories. 
AbstractFactory usually defines a different operation for each kind of 
product it can produce. The kinds of products are encoded in the operation 
signatures. Adding a new kind of product requires changing the AbstractFactory
interface and all the classes that depend on it.
A more flexible but less safe design is to add a parameter to operations
that create objects. This parameter specifies the kind of object to be
created. It could be a class identifier, an integer, a string, or anything
else that identifies the kind of product. In fact with this approach,
AbstractFactory only needs a single "Make" operation with a parameter
indicating the kind of object to create. This is the technique used in the
Prototype- and the class-based abstract factories discussed earlier.
This variation is easier to use in a dynamically typed language like
Smalltalk than in a statically typed language like C++. You can use it in
C++ only when all objects have the same abstract base class or when the
product objects can be safely coerced to the correct type by the client
that requested them. The implementation section of Factory Method (121)
shows how to implement such parameterized operations in C++.
But even when no coercion is needed, an inherent problem remains: All
products are returned to the client with the same abstract interface as
given by the return type. The client will not be able to differentiate or
make safe assumptions about the class of a product. If clients need to
perform subclass-specific operations, they won't be accessible through the
abstract interface. Although the client could perform a downcast (e.g.,
with dynamic_cast in C++), that's not always feasible or safe, because the
downcast can fail. This is the classic trade-off for a highly flexible and
extensible interface.

KNOWN USES

InterViews uses the "Kit" suffix to denote AbstractFactory classes. It
defines WidgetKit and DialogKit abstract factories for generating
look-and-feel-specific user interface objects. InterViews also includes a
LayoutKit that generates different composition objects depending on the layout
desired. For example, a layout that is conceptually horizontal may require
different composition objects depending on the document's orientation (portrait
or landscape).
ET++ [WGM88] uses the Abstract Factory pattern to achieve portability across
different window systems (X Windows and SunView, for example). The WindowSystem
abstract base class defines the interface for creating objects that represent
window system resources (MakeWindow, MakeFont, MakeColor, for example). Concrete
subclasses implement the interfaces for a specific window system. At run-time,
ET++ creates an instance of a concrete WindowSystem subclass that creates concrete
system resource objects.

RELATED PATTERNS

AbstractFactory classes are often implemented with factory methods, but they can 
also be implemented using Prototype. 
A concrete factory is often a singleton.