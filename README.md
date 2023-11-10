# HW3 Implement FAE in your favorite langauge

## How to Compile and Run

### In Eclipse
File → Import → Existing Projects into Workspace → Next → Select archive file → Browse → Choose FAE.zip → Finish -> Run -> Run Configurations -> Select Arguments tab

##### Enable only parser
ex1. -p "{with {x {- 5 3}} {+ x {- x 1}}}"

##### Enable interpreter
ex1. "{with {x {- 5 3}} {+ x {- x 1}}}"

### In Terminal
1. Move to src directory and check if bin directory exists. bin directory contains .class files.
2. Compile
javac -d [directory containing .class files] [class path where the main method exists] [.java file_that_has_main_method] [class path where other java files exists] [all .java files under that class path]\
ex1. **javac -d bin edu/handong/csee/plt/Main.java** **edu/handong/csee/plt/ast/*.java** **edu/handong/csee/plt/defsub/*.java** **edu/handong/csee/plt/retval/*.java**
4. Run
java -cp [directory containing .class files] [class path where the main method exists] [class name_that_has_main_method] [<option.>] ["<FAE.>"]

* <option> not always required
* <FAE> concrete syntax of FAE type

##### Enable only parser
set <option.> to '-p'.\
ex1. java -cp bin edu/handong/csee/plt/Main **-p** "{with {x {- 5 3}} {+ x {- x 1}}}"

##### Enable interpreter
do not set <option.>.\
ex1. java -cp bin edu/handong/csee/plt/Main "{with {x 10} {+ 4 5}}"

## Java files information
* **'edu/handong/csee/plt' package**
  * Main.java\
    'main' method exists here
  * Parser.java\
    concrete syntax -> abstract syntax
  * Interpreter.java\
    abstract syntax -> corresponding result (FAEValue type)
  * Lookup.java\
    For the given symbol, find the corresponding value in the DefrdSub cache. If there is no symbol return free identifier error.

* **'edu/handong/csee/plt/ast' package**
  * Add.java
  * App.java
  * AST.java
  * Fun.java
  * Id.java
  * Num.java
  * Sub.java
  
* **'edu/handong/csee/plt/defsub' package**
  * ASub.java
  * DefrdSub.java
  * MtSub.java

* **'edu/handong/csee/plt/retval' package**
  * ClosureV.java
  * FAEValue.java
  * NumV.java

## FAE: BNF
<FAE.> :: = <num.>\
            | {+ <FAE.> <FAE.>}\
            | {- <FAE.> <FAE.>}\
            | <id.>\
            | {fun {<id.> <FAE.>}}\
            | {<FAE.> <FAE.>}

##  Testcase

**self-made examples**

**ex1. {with {x 5} {+ x {- x 1}}}**\
input : "{with {x 5} {+ x {- x 1}}}"\
output : (numV 9)

**ex2. {with {x 10} {- 14 5}}**\
input : "{with {x 10} {- 14 5}}"\
output : (numV 9)

**ex3. {+ x {- 2 1}}**\
input : "{+ x {- 2 1}}"\
output : lookup: free identifier

**ex4. {with {x 1} {+ 3 {{fun {f} {+ f x}} 2}}}**\
input : "{with {x 1} {+ 3 {{fun {f} {+ f x}} 2}}}"\
output : (numV 6)

**ex5. {with {y 1} {+ {with {y 3} y} y}}**\
input : "{with {y 1} {+ {with {y 3} y} y}}"\
output : (numV 4)

**ex6. {with {x 5} {with {y 3} {+ y x}}}**\
input : "{with {x 5} {with {y 3} {+ y x}}}"\
output : (numV 8)

**examples from PPT slides and lectures**

**ex1. {with {x 3} {with {f {fun {y} {+ x y}}} {with {x 5} {f 4}}}}**\
input : "{with {x 3} {with {f {fun {y} {+ x y}}} {with {x 5} {f 4}}}}"\
output : (numV 7)

**ex2. {with {y 10} {fun {x} {+ y x}}}**\
input : "{with {y 10} {fun {x} {+ y x}}}"\
output : (closureV 'x (add (id 'y) (id 'x)) (aSub 'y (numV 10) (mtSub)))

**ex3. {with {y 3} {with {z {fun {x} {+ x y}}} {with {y 10} z}}}**\
input : "{with {y 3} {with {z {fun {x} {+ x y}}} {with {y 10} z}}}"\
output : (closureV 'x (add (id 'x) (id 'y)) (aSub 'y (numV 3) (mtSub)))

**ex4. {with {z {fun {x} {+ x y}}} {with {y 10} z}}**\
input : "{with {z {fun {x} {+ x y}}} {with {y 10} z}}"\
output : (closureV 'x (add (id 'x) (id 'y)) (mtSub))

**ex5. {with {y 3} {with {z {fun {x} {+ x y}}} {with {y 10} {z 5}}}}**\
input : "{with {y 3} {with {z {fun {x} {+ x y}}} {with {y 10} {z 5}}}}"\
output : (numV 8)

**ex6. {with {z {fun {x} {+ x y}}} {with {y 10} {z 5}}}**\
input : "{with {z {fun {x} {+ x y}}} {with {y 10} {z 5}}}"\
output : lookup: free identifier
