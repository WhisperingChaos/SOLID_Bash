# SOLID_Bash
Reimagine [GNU Bash](https://www.gnu.org/software/bash/) applying SOLID principles from a golang influenced perspective.

### SOLID Principles
If unfamiliar, read [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)).

How?
+ Single Responsibility - Separate concerns into separate bash source files.  Use bash [source](https://en.wikipedia.org/wiki/Source_(command)) command to compose a Component.   
+ Open/Close - Bash source files are open to extension but closed to modification.  Instead of directly altering the content of a source file employ Bash's function overriding(#function-overriding) mechanism to replace a function's provided behavior with the desired one.
+ Liskov substitution principle - What could one do with function overriding and an associative array?  Look to python for an answer...  Bash provides both mechanisms since its 4.0 release.  The combination of these language features enables the encoding of OO features such as virtual functions, method inheritance, and encapsulation of data members. 
+ Interface segregation principle - see Single Responsibility above, as separating concerns into separate bash source files partitions a Component's composite interface.
+ Dependency inversion principle - One can certainly create an interface/abstaction and apply function overriding, mentioned when discussing Open/Close, to realize inversion. 

### Function Overriding ###
A function's implementation can be replaced by creating another function with exactly the same name but whose body contains a different implementation.  The effect of overriding can occur:
+ "Statically" when lexically positioning the overriding function declaration.  The overriding declaration must appear lexically after its intended target as bash, if not diverted, executes statements starting from the top of a script's file to its bottom.  Given this order bash first interprets the function targeted for override then after further processing the file, it encounters the subsequent duplicate declaration and replaces the targeted function's body with the one defined by the subsequent one, thereby overriding the first (prior) declaration. 
+ "Dynamically" during run time.  Bash permits one to define/declare a function and its body inside another function or execute the [```source```](https://www.gnu.org/software/bash/manual/bash.html) statement.  When declared within an encapsulating function (nested), a call to that function will cause bash to process the nested function declaration.  As it's processing the nested declaration, bash will include this function in its global namespace.  If a function already exists with the same name, bash replaces the existing function body with the new one.  The ```source``` statement achieves the same outcome via similar behavior.  However, this mechanism essentially encapsulates the overriding function(s) inside a file that's separate from the one that executes the ```source``` statement. 
Note function overriding solely relies on the function's name.  It's up to the developer to ensure the overriding function consumes the same parameter list in the same order as its target.  Although the discussion above categorizes the overriding behavior as static/dynamic, there's only one mechanism - the dynamic one.  Since bash is interpreted as it executes, a script's execution behavior dictates when its function declarations are processed.  Therefore, lexical ordering emerges only when bash executes declarations in the lexical order defined within a script.
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
msg__basic(){
...
    local pasFrameDetail  # passed variable to receive output of call
   	msg__call_frame "$msgCallerFrame" 'pasFrameDetail'
    # pasFrameDetail contains human readable call stack information
...
}

msg__call_frame(){
    local -ri callLvl=$1
    local -r rtnCallFrame="$2" # value is now set to 'pasFrameDetail'
   
    local  callFrame=" file='${BASH_SOURCE[$callLvl+2]}'"
    callFrame="$callFrame lineNo=${BASH_LINENO[$callLvl+1]}"
    callFrame="$callFrame func='${FUNCNAME[$callLvl+2]}'"
    # use of eval to set the value of the passed 'pasFrameDetail' variable to readable
    # call stack information to return it to the caller. 
    eval $rtnCallFrame\=\"\$callFrame\" 
}

 ``` 
  
  
### Component Composition

[Component Composition](https://github.com/WhisperingChaos/sourcer.sh/blob/master/component/base/sourcer.source.sh)


### Terms
#### Public Element
A Bash language element, such as a function, that can be directly referenced by expressions declared in source files external to the one containing its definition.
#### Private Element
A Bash language element, such as an environment varialbe, that must only be directly referenced by expressions declared in the source file that contains its definition.
