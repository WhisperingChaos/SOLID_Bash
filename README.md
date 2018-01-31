# SOLID_Bash
Reimagine [GNU Bash]() applying SOLID principles from a golang influenced perspective.

### SOLID Principles
If unfamiliar, read [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)).

How?
+ Single Responsibility - Separate concerns into separate bash source files.  Use bash [source](https://en.wikipedia.org/wiki/Source_(command)) command to compose a Component.   
+ Open/Close - Bash source files are open to extension but closed to modification.  Instead of directly altering the content of a source file employ Bash's function overriding mechanism to replace a function's provided behavior with the desired one.
+ Liskov substitution principle - What could one do with function overriding and an associative array?  Look to python for an answer...  There's
+ Interface segregation principle - see Single Responsibility above, as separating concerns into separate bash source files partitions a Component's composite interface.
+ Dependency inversion principle - One can certainly create an interface/abstaction and apply function overriding, mentioned when discussing Open/Close, to realize inversion. 

 ### Source File Conventions
 + Decide on a meanginful prefix that will be assigned to all elements declared in a Bash source file.  This prefix behaves like a namespace or golang package name.  Prefixes are always terminated by an underscore.
 Example:
 The "msg_" prefix 
