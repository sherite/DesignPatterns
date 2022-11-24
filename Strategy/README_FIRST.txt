STRATEGY - BEHAVIORAL DESIGN PATTERN

INTENT

Define a family of algorithms, encapsulate each one, and make theminterchangeable. 
Strategy lets the algorithm vary independently fromclients that use it. 

ALSO KNOWN AS
POLICY

MOTIVATION
Many algorithms exist for breaking a stream of text into lines.Hard-wiring all 
such algorithms into the classes that require themisn't desirable for several 
reasons: 
· Clients that need linebreaking get more complex if they includethe 
linebreaking code. That makes clients bigger and harder tomaintain, 
especially if they support multiple linebreaking algorithms. 
· Different algorithms will be appropriate at different times. We don'twant 
to support multiple linebreaking algorithms if we don't use themall. 
· It's difficult to add new algorithms and vary existing ones whenlinebreaking 
is an integral part of a client. 
We can avoid these problems by defining classes that encapsulatedifferent 
linebreaking algorithms. An algorithm that's encapsulated in this way is called 
a strategy. 

Suppose a Composition class is responsible for maintaining andupdating the 
linebreaks of text displayed in a text viewer.Linebreaking strategies aren't 
Design Patterns: Elements of Reusable Object-Oriented Software 
350 
implemented by the class Composition.Instead, they are implemented separately 
by subclasses of the abstractCompositor class. Compositor subclasses implement 
different strategies: 
· SimpleCompositorimplements a simple strategy that determines linebreaks 
one at atime. 
· TeXCompositorimplements the TeX algorithm for finding linebreaks. This 
strategytries to optimize linebreaks globally, that is, one paragraph at 
atime. 
· ArrayCompositorimplements a strategy that selects breaks so that each row 
has a fixednumber of items. It's useful for breaking a collection of icons 
intorows, for example. 
A Composition maintains a reference to a Compositor object. Whenever aComposition 
reformats its text, it forwards this responsibility to itsCompositor object. The 
client of Composition specifies whichCompositor should be used by installing the 
Compositor it desires intothe Composition. 

APPLICABILITY

Use the Strategy pattern when 
· many related classes differ only in their behavior. Strategiesprovide a 
way to configure a class with one of many behaviors. 
· you need different variants of an algorithm. For example, you might 
definealgorithms reflecting different space/time trade-offs.Strategies 
can be used when these variants are implemented as a classhierarchy of 
algorithms [HO87]. 
· an algorithm uses data that clients shouldn't know about. Use theStrategy 
pattern to avoid exposing complex, algorithm-specific datastructures. 
· a class defines many behaviors, and these appear as multipleconditional 
statements in its operations. Instead of manyconditionals, move related 
conditional branches into their ownStrategy class.

STRUCTURE

PARTICIPANTS

· Strategy (Compositor) 
o declares an interface common to all supported algorithms. Context 
uses this interface to call the algorithm defined by a 

ConcreteStrategy. 
· ConcreteStrategy (SimpleCompositor, TeXCompositor,ArrayCompositor) 
o implements the algorithm using the Strategy interface. 

· Context (Composition) 
o is configured with a ConcreteStrategy object. 
o maintains a reference to a Strategy object. 
o may define an interface that lets Strategy access its data.


COLLABORATIONS

· Strategy and Context interact to implement the chosen algorithm. Acontext 
may pass all data required by the algorithm to the strategywhen the algorithm 
is called. Alternatively, the context can passitself as an argument to 
Strategy operations. That lets the strategycall back on the context as 
required. 

· A context forwards requests from its clients to its strategy. Clientsusually 
create and pass a ConcreteStrategy object to the context;thereafter, 
clients interact with the context exclusively. There isoften a family of 
ConcreteStrategy classes for a client to choosefrom.

CONSEQUENCES

The Strategy pattern has the following benefits and drawbacks:

1. Families of related algorithms.Hierarchies of Strategy classes define a 
family of algorithms orbehaviors for contexts to reuse. Inheritance canhelp 
factor out common functionality of the algorithms. 

2. An alternative to subclassing.Inheritance offers another way to support 
a variety of algorithms orbehaviors. You can subclass a Context class 
directly to give itdifferent behaviors. But this hard-wires the behavior 
into Context.It mixes the algorithm implementation with Context's, making 
Contextharder to understand, maintain, and extend. And you can't vary 
thealgorithm dynamically. You wind up with many related classes whoseonly 
difference is the algorithm or behavior they employ.Encapsulating the 
algorithm in separate Strategy classes lets you varythe algorithm 
independently of its context, making it easier toswitch, understand, and 
extend. 

3. Strategies eliminate conditional statements.The Strategy pattern offers 
an alternative to conditional statements forselecting desired behavior. 
When different behaviors are lumped into oneclass, it's hard to avoid using 
conditional statements to select theright behavior. Encapsulating the 
behavior in separate Strategy classeseliminates these conditional 
statements. 

4. A choice of implementations.Strategies can provide different 
implementations of the samebehavior. The client can choose among strategies 
with differenttime and space trade-offs. 

5. Clients must be aware of different Strategies.The pattern has a potential 
drawback in that a client must understandhow Strategies differ before it 
can select the appropriate one.Clients might be exposed to implementation 
issues. Therefore youshould use the Strategy pattern only when the variation 
in behavior isrelevant to clients. 

6. Communication overhead between Strategy and Context.The Strategy interface 
is shared by all ConcreteStrategy classeswhether the algorithms they 
implement are trivial or complex. Henceit's likely that some 
ConcreteStrategies won't use all the informationpassed to them through this 
interface; simple ConcreteStrategies mayuse none of it! That means there 
will be times when the contextcreates and initializes parameters that never 
get used. If this is anissue, then you'll need tighter coupling between 
Strategy and Context. 

7. Increased number of objects.Strategies increase the number of objects in 
an application. Sometimesyou can reduce this overhead by implementing 
strategies as statelessobjects that contexts can share. Any residual state 
is maintained by thecontext, which passes it in each request to the Strategy 
object. Sharedstrategies should not maintain state across invocations. The 
Flyweight (218) pattern describes this approach in moredetail

IMPLEMENTATION

Consider the following implementation issues: 

1. Defining the Strategy and Context interfaces.The Strategy and Context 
interfaces must give a ConcreteStrategyefficient access to any data it needs 
from a context, and vice versa. 
One approach is to have Context pass data in parameters to 
Strategyoperations—in other words, take the data to the strategy. This 
keepsStrategy and Context decoupled. On the other hand, Context mightpass 
data the Strategy doesn't need. 
Another technique has a context pass itself as an argument, andthe strategy 
requests data from the context explicitly.Alternatively, the strategy can 
store a reference to its context,eliminating the need to pass anything at 
all. Either way, thestrategy can request exactly what it needs. But now
Context mustdefine a more elaborate interface to its data, which couples 
Strategyand Context more closely. 
The needs of the particular algorithm and its data requirements 
willdetermine the best technique. 

2. Strategies as template parameters.In C++ templates can be used to configure 
a class with a strategy.This technique is only applicable if (1) the Strategy 
can be selectedat compile-time, and (2) it does not have to be changed at 
run-time.In this case, the class to be configured (e.g., Context) isdefined 
as a template class that has a Strategy class as a parameter.
The class is then configured with a Strategy class when it's 
instantiatedWith templates, there's no need to define an abstract class that defines 
the interface to the Strategy. Using Strategy as atemplate parameter also 
lets you bind a Strategy to itsContext statically, which can increase 
efficiency. 

3. Making Strategy objects optional.The Context class may be simplified if 
it's meaningful not tohave a Strategy object. Context checks to see if it 
has a Strategyobject before accessing it. If there is one, then Context 
uses itnormally. If there isn't a strategy, then Context carries out 
defaultbehavior. The benefit of this approach is that clients don't have 
todeal with Strategy objects at all unless they don't like thedefault 
behavior. 

RELATED PATTERNS

Flyweight: Strategy objects often make good flyweights.