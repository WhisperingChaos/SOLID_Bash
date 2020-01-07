# SOLID_Bash
Reimagine [GNU Bash]() applying SOLID principles from a golang influenced perspective.

### SOLID Principles
If unfamiliar, read [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)).

How?
+ Single Responsibility - Separate concerns into separate bash source files.  Use bash [source](https://en.wikipedia.org/wiki/Source_(command)) command to compose a Component.   
+ Open/Close - Bash source files are open to extension but closed to modification.  Instead of directly altering the content of a source file employ Bash's function overriding mechanism to replace a function's provided behavior with the desired one.
+ Liskov substitution principle - What could one do with function overriding and an associative array?  Look to python for an answer...  Bash provides both mechanisms since its 4.0 release.  The combination of these language features enables the encoding of OO features such as virtual functions, method inheritance, and encapsulation of data members. 
+ Interface segregation principle - see Single Responsibility above, as separating concerns into separate bash source files partitions a Component's composite interface.
+ Dependency inversion principle - One can certainly create an interface/abstaction and apply function overriding, mentioned when discussing Open/Close, to realize inversion. 

 ### Source File Conventions
 + Decide on a meanginful prefix, representing a cohesive responsibility, that will be assigned to all elements declared in a Bash source file.  This prefix behaves like a namespace or golang package name.  Prefixes are always terminated by an underscore.
 
    Example
    
    See [msg.source.sh](https://github.com/WhisperingChaos/msg.source.sh/blob/master/component/msg.source.sh).  This bash file cohesively packages a logging interface and default implement that can be called by other Bash functions. Note that every function and other bash programming elements, like global ENVIRONMENT variables, are prefixed by "msg_".

  + Create a source file to contain these cohesive elements.  The source file name must be a combination of the prefix, less the trailing underscore, concatenated with ```.source.sh```.  This naming convention is important to the [Component Composition](#component-composition) process. 
  
    Example
    
    See [msg.source.sh](https://github.com/WhisperingChaos/msg.source.sh/blob/master/component/msg.source.sh).
    
  + [Public Element](#public-element)s in a source file simply begin with a prefix, followed by the element's name.
  
    Example
  ```
  msg_inform(){
  ...
  }
  ```
  + [Private Element](#private-element)s in a source file begin with a prefix, followed by an element's name, however, the element's name begins with an underscore ("_").
  
    Example
  ```
  msg__basic(){
  ...
  }
  ```
  
  ### Programming Conventions
  + Argument lists are documented by local variables (see Bash [declare](https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#Bash-Builtins)) specified immediately after a function's declaration.  When possible declare the variable to be immutable via ```local -r```.  Also when applicable, use local's -i option to differenciate numeric arguments from usual untyped string ones.
  
    Example
    ```
    msg__basic(){
        local -r msg="$1"
        local -r msgType="$2"
        local -r outputFile="$3"
        local -ri msgCallerFrame="$4"
        ...
    }
    ```
    
  + Although not explicitly supported, local variable names can be passed to a function enabling the function to return its output(s) to the caller, similar to call by reference mechanism supported by most other programming languages.  This mechanism can be simulated by passing a the name of a caller's varible to the called function then before returning to the caller, the function assigns the passed variable name the computed output value via [eval](https://tiswww.case.edu/php/chet/bash/bashref.html#Bourne-Shell-Builtins).
  
  Example
  ```
  ```
  
  
### Component Composition

[Component Composition](https://github.com/WhisperingChaos/sourcer.sh/blob/master/component/base/sourcer.source.sh)


### Terms
#### Public Element
A Bash language element, such as a function, that can be directly referenced by expressions declared in source files external to the one containing its definition.
#### Private Element
A Bash language element, such as an environment varialbe, that must only be directly referenced by expressions declared in the source file that contains its definition.
