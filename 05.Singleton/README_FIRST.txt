SINGLETON - CREATIONAL DESIGN PATTERN

INTENT

Ensure a class only has one instance, and provide a global point of access to it.

MOTIVATION

It's important for some classes to have exactly one instance. Although there can
be many printers in a system, there should be only one printer spooler. There
should be only one file system and one window manager. A digital filter will have
one A/D converter. An accounting system will be dedicated to serving one company.
How do we ensure that a class has only one instance and that the instance is easily
accessible? A global variable makes an object accessible, but it doesn't keep
you from instantiating multiple objects.
A better solution is to make the class itself responsible for keeping track of
its sole instance. The class can ensure that no other instance can be created
(by intercepting requests to create new objects), and it can provide a way to
access the instance. This is the Singleton pattern.

APPLICABILITY

Use the Singleton pattern when
· there must be exactly one instance of a class, and it must be accessible
to clients from a well-known access point.
· when the sole instance should be extensible by subclassing, and clients
should be able to use an extended instance without modifying their code.


STRUCTURE

PARTICIPANTS

· Singleton

o defines an Instance operation that lets clients access its unique
instance. Instance is a class operation.

o may be responsible for creating its own unique instance.

COLLABORATIONS

· Clients access a Singleton instance solely through Singleton's Instance
operation.

CONSEQUENCES

The Singleton pattern has several benefits:

1. Controlled access to sole instance. Because the Singleton class
encapsulates its sole instance, it can have strict control over how and
when clients access it.

2. Reduced name space. The Singleton pattern is an improvement over global
variables. It avoids polluting the name space with global variables that
store sole instances.

3. Permits refinement of operations and representation. The Singleton class
may be subclassed, and it's easy to configure an application with an instance
of this extended class. You can configure the application with an instance
of the class you need at run-time.

4. Permits a variable number of instances. The pattern makes it easy to change
your mind and allow more than one instance of the Singleton class. Moreover,
you can use the same approach to control the number of instances that the
application uses. Only the operation that grants access to the Singleton
instance needs to change.

5. More flexible than class operations. Another way to package a singleton's
functionality is to use class operations. But both of these language techniques
make it hard to change a design to allow more than one instance of a class.

IMPLEMENTATION

Here are implementation issues to consider when using the Singleton pattern:

1. Ensuring a unique instance. The Singleton pattern makes the sole instance
a normal instance of a class, but that class is written so that only one
instance can ever be created. A common way to do this is to hide the operation
that creates the instance behind a class operation (that is, either a static
member function or a class method) that guarantees only one instance is
created. This operation has access to the variable that holds the unique
instance, and it ensures the variable is initialized with the unique
instance before returning its value. This approach ensures that a singleton
is created and initialized before its first use.
You can define the class operation in C++ with a static member function
Instance of the Singleton class. Singleton also defines a static member
variable _instance that contains a pointer to its unique instance.
Clients access the singleton exclusively through the Instance member
function. The variable _instance is initialized to 0, and the static member
function Instance returns its value, initializing it with the unique
instance if it is 0. Instance uses lazy initialization; the value it returns
isn't created and stored until it's first accessed.
Notice that the constructor is protected. A client that tries to instantiate
Singleton directly will get an error at compile-time. This ensures that
only one instance can ever get created.
Moreover, since the _instance is a pointer to a Singleton object, the
Instance member function can assign a pointer to a subclass of Singleton
to this variable. We'll give an example of this in the Sample Code.
There's another thing to note about the C++ implementation. It isn't enough
to define the singleton as a global or static object and then rely on
automatic initialization. There are three reasons for this:

1. We can't guarantee that only one instance of a static object will
ever be declared.

2. We might not have enough information to instantiate every singleton
at static initialization time. A singleton might require values that
are computed later in the program's execution.

3. C++ doesn't define the order in which constructors for global objects
are called across translation units [ES90]. This means that no
dependencies can exist between singletons; if any do, then errors
are inevitable.
An added (albeit small) liability of the global/static object approach is
that it forces all singletons to be created whether they are used or not.
Using a static member function avoids all of these problems.

2. Subclassing the Singleton class. The main issue is not so much defining
the subclass but installing its unique instance so that clients will be
able to use it. In essence, the variable that refers to the singleton
instance must get initialized with an instance of the subclass. The simplest
technique is to determine which singleton you want to use in the Singleton's
Instance operation. An example in the Sample Code shows how to implement
this technique with environment variables.
Another way to choose the subclass of Singleton is to take the implementation
of Instance out of the parent class (e.g., MazeFactory) and put it in the
subclass. That lets a C++ programmer decide the class of singleton at
link-time (e.g., by linking in an object file containing a different
implementation) but keeps it hidden from the clients of the singleton.
The link approach fixes the choice of singleton class at link-time, which
makes it hard to choose the singleton class at run-time. Using conditional
statements to determine the subclass is more flexible, but it hard-wires
the set of possible Singleton classes. Neither approach is flexible enough
in all cases.
A more flexible approach uses a registry of singletons. Instead of having
Instance define the set of possible Singleton classes, the Singleton classes
can register their singleton instance by name in a well-known registry.
The registry maps between string names and singletons. When Instance needs
a singleton, it consults the registry, asking for the singleton by name.
The registry looks up the corresponding singleton (if it exists) and returns
it. This approach frees Instance from knowing all possible Singleton classes
or instances. All it requires is a common interface for all Singleton classes
that includes operations for the registry

RELATED PATTERNS

Many patterns can be implemented using the Singleton pattern. See Abstract Factory,
Builder, and Prototype.