---
description: 'the language included are scheme, c, java, scala, javascript'
---

# CSC 447 review

## Lecture 1

### class notes

#### overview

* machines and programs
  * machines=input simulation+output responce
  * program:perticular responce and store in memory
    * computing=control and memory
    * fixed program before computuation begins,and store in the memory
  * time
    * before program: compile time
    * after program:run tiime
  * finer grained
    * compiler: compile,link, load
    * memory: allocation, initialization, deallocation
    * function: call, return
  * memory
    * copying and sharing
    * allcation and deallocation
    * categories
      * globe
      * local
      * unmamanaged
      * ARC and garbage collection
* interpretation and translation
  * machine language to program language
  * transforamed into equivalent program: c\# to visual machine language
* designing a language
  * resource constraints
  * programming model
  * error
  * module

#### c:statements versus expression, strict versus non strict and undefined behavior

* statement and expression
  * statements:executed for side-effects, eg: literal,operators,function call
  * expression:executed for their value\(for side effect\),eg:loop ifelse, return and expression statements\(include assignment\)
  * PS: side-effect eg: x++, x plus 1 and change x value
    * side-effecting expression:x++
* comma operator \(e1,e2,e3\)
* sequencing:

```text
1
int x=5;
x*=2;
print (x)

2
int x=5;
print("%d\n",(x*=2,x))
the semantics is not same,but result is same
```

* strict and non strict
  * strict: if it evaluate all of its operands before it runst
    * eg: operators:+,-,\*,/ \| comparson:&lt;,&lt;=,==, != \| bitwise:\|,&&lt;,!,^\|function call\| etc

```c
int fcond (int b, int t, int f) { return b ? t : f; }//function
int main () {
  for (int i=0; i<10; i++) {
    int x = 0;
    int y = 0;
    int z = fcond (rand()%2,  (x=1,111), (y=2,222));
    printf ("x=%d, y=%d, z=%d\n", x, y, z);
  }
}

outout:
x=1, y=2, z=111
x=1, y=2, z=222
```

* non strict:evaluate part of its operands
  * eg &&,\|\|,e1?e2:e3 \(ternary operator\)\| macro expansion 

```c
define mcond(b, t, f) (b)?(t):(f) //macro expansion
int main () {
  for (int i=0; i<10; i++) {
    int x = 0;
    int y = 0;
    int z = mcond (rand()%2,  (x=1,111), (y=2,222));
    printf ("x=%d, y=%d, z=%d\n", x, y, z);
  }
}
output:
x=1, y=0, z=111
x=0, y=2, z=222
```

Because function is strict, before execute function mcond, x=1, and y=2, z=111or 222. Macro call is not strict, so that the function mcond execute first when meeting b go back find the parameter b, random/2!= 0 true, and meeting t, t=1,111 and ignore y=2, 222. macro calls are evaluated in the compiler, by textual substitution.

* conditional  statment and conditional expression
  * conditional statement

```text
int fact(int n){
    if(n<1){
        return 1;
    }
    else{
       return n*fact(n-1); 
    }
}
```

* conditional expression

```text
int fact(int n){
    return (n<1)?1:n*fact(n-1);
}
```

Because  conditional expression use expression and didn't use statement.

* undefined behavior
  * under/overflow

```text
#include <stdio.h>

int isMinValue (int x) {
  return (x-1) > x;
}
int main () {
  int i = -2000000000;  //c no boolean type  integer 0 or 1
  while (!isMinValue(i))
    i--;
  printf ("Min value is %d\n", i);
}
 output1:
 $ gcc -O1 undefined.c && ./a.out 
Min value is -2147483648
 
```

```text
$ gcc -O2 undefined.c && ./a.out 
^C #infinite loop
```

O1 explanation: when execute isMinValue\(x\): i-1, and compare with x, if x-1 &gt;x  return 1, else return 0, -2000000000-1-1-1-1...,x-1 always less than x, return false. until i=-2,147,483,648, which minus 1 is underflow, the result is 2147483647. the function return value is true, and print -2,147,483,648 is min value.

O2 explanation: the result is infinit loop, because the underflow in c is undefined behavior, what the compiler do is x-1&gt;x, is always true,so it is infinit loop.

* order of operations

```text
#include <stdio.h>
int count = 0;
int f () {
  count += 1;
  return count;
}
int main () {
  int z = f() + f();
  printf ("%d\n", z);
  z = (z += 1) + (z = z*z);
  printf ("%d\n", z);
}
 output：
$ clang -Wall undefined3.c 
undefined3.c:11:21: warning: unsequenced modification and access to 'z'
  z = (z += 1) + (z = z*z);
         ~~         ^
1 warning generated.
$ ./a.out 
3
20
```

it runs in clang, the compiler gives a warning because it is not run in the right order, the sequence of run first f\(\) and second f\(\) is random, depends on compile. Thus it is undefined behavior.

* dangling pointers

```c
#include <stdio.h>
#include <stdlib.h>

int* foo (int n) {
  int result = 2 * n;
  return &result;
}

int main () {
  int* p = foo (5);
  int* q = foo (7);
  printf ("%x %d\n", p, *p);
  printf ("%x %d\n", q, *q);  
}
output: 
in clang: 
14 and big number
in gcc:
segment error
```

p and q in main function point into an activation record, which is stack, and when the function executing finished, the memory of that function will be deallocated.

#### scheme

* literal
  * number:5
  * String:"hello world"
  * symbol:'hello world'
* arithmetic 
  * using prefix notation: \(\* \(+ 1 2\) 3\)
* function
  * define: \(define \(square n\) \(\* n n\)\)
    * \(define \(f p1 p2 p3...\) \(e1 e2...\)\)
  * invoke: \(square 5 2\)
* boolean and condiaitonals
  * \#t or \#f
  * if is not strict
* recursive function
* con cells

  * pair of numbers:\(cons 1 2\)\| strings:\(cons "hello" "world"\)\| a number and  a string: \(cons 1 "world"\)
  * car: head \| cdr: tail
  * for linked list

    * empty list: \(\)
    * singleton list: \(cons 3 \(\)\)
    * regular list:\(cons 1 \(cons 2 \(cons 3 \(\)\)\)\)

  * list
    * list only has one structure type: the pair
    * a non-empty list is just a special type of pair
    * pairs are a kind of symbolic-expression

* syntax sugar for lists

  * quote: special form prevent evaluation: \(quote \(1 2 3\)\)
  * ' :shorthand for quote '\(1 2 3\)
  * list function evaluates args, put results in a list: \(list 2 3 \(+1 2\)\)

* equals problem
  * eq? compares two pointers are same, same to java ==
  * equal? compares two structure are same to java equals
* read-eval-print loop
  * quote:delay evaluation
  * eval: evaluates an expression
  * read function reads the expression

### worksheet

### homework

### quiz

## Lecture2

### class notes

* types：used to describe when operations allowed

  * "+" in java: string, numeric types
  * "-" in java: numeric types

* dynamic type checking and  static type checking
* **dynamic type checking**: tracks and stores type of data at runtime\(before applying an operation\).
  * how do we know this is dynamic? answer: no type checking before execution.
    * when writing an expression in function, invoke function there is no error message, but fails when call function.

```scheme
(define (f) (- 5 "hello"))  no error at here
(f)
output:
Error in -: expected type number, got '"hello"'.
```

* * Language use:
* **static type checking**:compiler analyzes code for type error
  * how do we know this is static; answer: javac and get the error message.

```java
class Typing01 {
  void f () {
    int a = 5;
    String b = "hello";
    System.out.println ("Result = " + (a - b));
  }
}

output:
$ javac Typing01.java
Typing01.java:5: error: bad operand types for binary operator '-'
    System.out.println ("Result = " + (a - b));
                                         ^
  first type:  int
  second type: String
```

* Language : C, Java, Scala, Haskell, Rust, Coq, etc

```c
int main () {
  char a[] = { 0x85, 0x86, 0x87, 0x88 };
  int b = a[0];
  char *p = &a[0];
  int *q = p;       /* type checker complains */
  int c = *q;
  printf ("b = %08x, p = %p, q = %p, c = %08x\n", b, p, q, c);
  return 0;
}
```

if char convert to int, it is allowed, but if the pointer of char convert to the pointer of int, it is not allowed.

Different from the code above,  we can use upcast, it gets this to compile but a\[0\] will be wrong value.

```c
int main () {
  char a[] = { 0x85, 0x86, 0x87, 0x88 };
  int b = a[0];
  char *p = &a[0];
  int *q = (int *) p;  /* type checker accepts explict cast */
  int c = *q;
  printf ("b=%08x, c=%08x, p=%p, q=%p\n", b, c, p, q);
  printf ("b=%d, c=%d\n", b, c);
  return 0;
}

output:
clang -m32 typing-02.c && ./a.out
b=ffffff85, c=88878685, p=0x7ffee09a01e8, q=0x7ffee09a01e8
b=-123, c=-2004384123
```

In the code,   char pointer \*p upcast to int pointer \*q, but and store the value of p in variable c. but even it doesn't get compiler error, the result is wrong.

* **shape error: the same memory location is read and interpreted at an imcompatible type, called unchecked runtime type error**
  * cause by upcasting, downcasting, ~~unproper assignment~~\(it can be checked when compile time\)
  * shape error are silent and allow access.

```c
void update (int* p) {
    *(p+1) = 0xf0800000;
}
int main () {
    float f = 10;    
    int a[]  = { 10 };
    int i = 10;
                printf ("f=%f, a[0]=%d i=%d\n", f, a[0], i);
    a[-1] = 47; printf ("f=%f, a[0]=%d i=%d\n", f, a[0], i);
    update (a); printf ("f=%f, a[0]=%d i=%d\n", f, a[0], i);
}

output:
clang -m32 typing-03.c && ./a.out
f=10.000000 a[0]=10 i=10
f=10.000000 a[0]=10 i=47
f=-316912650057057350374175801344.000000 a[0]=10 i=47
(from high to low: a[1], a[0], a[-1])
```

When stack of main has 3 variables, f, a\[\], and i, when a\[-1\] is assigned, it change i, and when executing update function, the address of a is as parameter of the function the address a+1 is changed, which original belong to i, so i is changed.

* strong and weak
  * strong type guarantees no shape errors\(upcasting or downcasting ,and array problem causes incompatible type\)
  * weak type may permit shape error

```text
void unsafeCommand () { printf ("ouch!\n"); }
void safeCommand ()   { printf ("hurray!\n"); }
int guess () { return &unsafeCommand - &safeCommand; }
int main () {
    printf ("%d\n", guess ());
    void (*c) () = &safeCommand;

    c();
    c += -48;
    c();
}

output:
clang -m32 typing-04.c && ./a.out
-48
hurray!
ouch!
```

UnsafeCommand function print ouch!, safeCommand function print hurray!, guess ,using ouch!-hurray!=-48, the main function print guess\(\) result, which is -48, and save safeCommand function in a pointer c. When invoking function c\(\), the result is hurray!, and add -48 to c\(\), the result is ouch!  In this way, if your guess function is correct you could call anything, super unsafe. 

😂: That is shape error: reading memory in an incompatible type or data used contrary to type. However c is weak type, so it could run and no warning and error message.

```
void floatCommand (float f) { printf ("f=%f\n", f); }
void intCommand (int i)     { printf ("i=%d\n", i); }
int guess () { return &floatCommand - (void (*)(float)) &intCommand; }
int main () {
    printf ("%d\n", guess ());
    void (*c) (int) = &intCommand;
    int j = 0xf0800000;
    c(j);
    c -= 64;
    c(j);
} 

output:
clang -m32 typing-05.c && ./a.out
-64
i=-260046848
f=-316912650057057350374175801344.000000
```

function floatCommand take a float number and return, intCommand function take a int and return, guess\(\) return the floatCommand address- upcasting intCommand address. In the main function guess return -64, and j is the number -260046848, intCommand function is store in a pointer c, c take a int number and return correct number, when c is try to become floatCommand, it return an incorrect number.

* Strong and static language
  * Java, C\#, Scala, Rust, etc
* weak  and static language
  * C
* Strong and dynamic language
  * Scheme, python, ruby, etc

😂: if a language is static type, it is hard to find upcasting and downcasting causes problem, but dynamic type     is easy to find. To compensate that problem, static type checking language also add dynamic type checking.

```text
class A { int x; }
class B extends A { float y; }
class C extends A { char c; }

class Typing02 {
  public static void main (String[] args) {
    B b = new B ();
    A a = b;
    C c = (C) a;
  }
}

output:
javac Typing02.java && java Typing02
Exception in thread "main" java.lang.ClassCastException: B cannot be cast to C
```

B and C extends A, So, B and C type object could upcast to A type, and A type Object could downcast to B and C type. In the main function, b is B type object and upcast to A type, then downcast to a type.  What we know is Java is static type language, but the exception message indicate Java also have dynamic type checking to make sure it was a C type before , thus, it could find a downcast to a is wrong.

Java doesn't allow shape error, so it is strong type. Java's type checking is mostly static, runtime tests for only in certain cases.

* java dynamic checks in java 

```text
class A { int x; }
class B extends A { float y; }
class C extends A { char c; }

class Typing03 {
  public static void main (String[] args) {
      B[] bs = new B[1];
      A[] as = bs;
      as[0] = new C();
      B b = bs[0];
  }
}

output:
javac Typing03.java && java Typing03
Exception in thread "main" java.lang.ArrayStoreException: C
```

the main function create a new array, B type with 2 length,  and upcast array to A type, as\[0\] is the first cell are defined to C type, it is not allowed, and set b to bs\[0\],which is as\[0\].One array contains two type of element, which is not allowed.

```text
class A { int x; }
class B extends A { float y; }
class C extends A { char c; }

class Typing04 {
  public static void main (String[] args) {
    B[] bs = new B[4];
    C c = new C();
    B b = bs[-1];
  }  
}

output:
javac Typing04.java && java Typing04
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: -1
```

Class B and C extend Class A. In main function,  A b type array are initialized,  and a variable c is c type, b  is bs\[-1\], which is out of the bound of array bs. 

```text
class A { int x; }
class B extends A { float y; }
class C extends A { char c; }

class Typing05 {
  static void f (B b) {
    A a = b;       // upcast
  }
  static void g (A a) {
    B b = (B) a;   // downcast
  }
  public static void main (String[] args) {
      f (new B());
      g (new C());
  }  
}

output:
javac Typing05.java && java Typing05
Exception in thread "main" java.lang.ClassCastException: C cannot be cast to B
```

function f is for upcasting, function g is for downcasting. In the main, g take a C type  parameter treated as A type, and down casting to B type, it is not allowed.

* 😂 dynamic checking in java: what is checking for? 

  * the error which is not syntax error and not operator error which static checking could detected
  * such as array out of bounds exception, dividing 0 exception, stack overflow or array overflow exception, which is not error but will cause problem. In c it is called undefined behavior.

* 😂 Why java restrict use of pointers, instead using garbage collection?
  * if use pointer, user have to free the memory to deallocate, which will reuse the memory using before and cause safety problem.

#### Scala introduction

* Scala： functional and object-oriented PL
  * Boolean, numeric, and string
  * static type checking
* variable:
  * immutable variable: val x=10
  * mutable variable: var x=10
* expression oriented:no statements, like scheme, ML, etc
* comma expression:{e\_1;e\_2;e\_3...;e\_n} or {e\_1 e\_2 e\_3... e\_n}
* method：
  * def plus\(x:Int, y:Int\): Int=x+y
  * def also could use to define global variable
* Object: everything is object in Scala
  * 5.toDouble
  * 5.+\(5\) or 5+6
  * 5.max\(6\) or 5 max 6
* tuple: Immuable
  * val x:\(Int, String\)=\(5,"hello"\)
* Linked List:varying number of homogeneous items
  * \(cons 1\(cons 2 \(\)\)\)
  * 1：：（2：：Nil）
  * List（1，2，3）
  * xs.head; xs.tail
  * wrong mistake: xs\(1\)=7
* pattern matching

```scala
//pattern matching
val p : (Int, String) = // ...
p match {
  case (x, y) => "Int is %d".format (x)
}
```

```text
val xs : List[Int] = // ...
xs match {
  case Nil   => "List is empty"
  case y::ys => "List is non-empty, head is %d".format (y)
}
```

recursive function

* functional programming has immutable programming, which is different from regular programming
* wildcard\_:relative ignore head

```scala
// length: input a list,return a int
//count
def length [X] (xs:List[X]) : Int = xs match {
  case Nil   => 0
  case _::ys => 1 + length (ys)
}
```

* List build in appending function
* \(:::\):append between list and list
* \(::\): append between element and list

### worksheet

### homework

### quiz

## Lecture3

### class notes

#### background knowledge:parametric polymorphism

* 😂why using generic type: for no need to overload one function for every type of parameter, the object in java could be used, but when specify type, we have to downcast and downcasting will cause shape error, so Java create generic type.
* generic linked list in C

```c
//Use (void *) type in C
typedef struct node_int_ptr node_int_ptr;

struct node_int_ptr {
  int *item;
  node_int_ptr *next;
};

int length_while_int_ptr (node_int_ptr *xs) {
  int result = 0;
  while (xs) {
    result += 1;
    xs = xs->next;
  }
  return result;
}
```

Use `(void *)` type in C,Can cast/store any pointer type at type `(void *)`But it must cast from `(void *)` before dereferencing. Problem on downcasting, which will cause shape error.

* generic type in Java
  * problem: Downcasting will cause shape error but never reach shape error

```java
static class Node {
  Object item;
  Node next;
}

Node xs = ...           // filled with java.io.InputStream elements
Object p = xs.item;     // OK
String q = (String) p;  // runtime ClassCastException halts execution
q.endsWith ("a");       // never reach shape error
```

* the right generic type: could use any type as parameter and don't need to downcast

```java
static class Node<X> {
  X item;
  Node<X> next;

  public Node (X item, Node<X> next) { 
    this.item = item; 
    this.next = next; 
  }
}

static <X> int length (Node<X> xs) {
  if (xs == null) {
    return 0;
  } else { 
    return 1 + length (xs.next);
  }
}
```

#### function programming

* function and method

```scala
def a (x:Int) = x + 1; //method a
val b = (x:Int) => x + 1; //anonymous function
val c : (Int => Int) = (x) => x + 1;// anonymous function with a parameter x
val d = new Function[Int,Int] { def apply(x:Int) = x + 1 } // function oject with apply method
object e extends Function1[Int, Int] { def apply(x:Int) = x + 1; }
val f : PartialFunction[Any, Int]  = { case i: Int ⇒ i + 1 }
```

function is an object, when executing function, function are implemented as object with method apply,  aka function will convert to method and execute\(function.apply\(\)\) .method is just method. 

* Build-in function use
  * for each is new control structure for list, based on for each to improve other function
  * foreach, sum, transform, map, identity,filter
* recursion, function could be a parameter, own function
  * pass named function\(printElt\) to foreach: foreach \(xs, printElt\)
    * xs.foreach\(\(x:X\)=&gt;println\(x\)\)
    * foreach\(xs,\(x:X\)=&gt;println\(x\)\)
  * pass anonymous function to foreach:foreach \(xs, \(x:Int\) =&gt; println \("%02d".format\(x\)\)\)
* foldleft and foldright understanding
  * sum to foldleft: use tail recursion

```scala
def sum(xs:List[Int],z:Int=0): Int=>{
    case Nil=>Nil
    case y::ys=> sum(ys,y+z)
}
val xs = List(11,21,31)
sum (xs)
```

* * FoldLeft: tail recursion

```scala
def foldleft [Z,X](z:Z,xs:List[X],f((Z,X)=>Z)): Z=>{
    case Nil=>Nil
    case y::ys=>foldleft(f(z,y),ys,f)
}
foldleft(0,xs,(z:Int,x:Int)=>z+x)
```

* * sum to foldright:non-tail recursion

```scala
def sum2 (xs:List[Int],z:Int=0):Int=>{
    case Nil=>Nil
    case y::ys=>y+sum(ys,z)
}
val xs = List(11,21,31)
sum (xs)
```

* * FoldRight: non-tail recursion

```scala
def foldright [Z,X] (z:Z,xs:List[X],f(Z,X)=>Z):Z={
    case Nil=>Nil
    case y::ys=>f(z,foldright(z,ys,f))
}
foldright(0,xs,(z:Int,x:Int)=>z+x)
```

* * foldleft and foldright:
    * `foldLeft` is tail recursive: each iteration applies `f` to the next value and the accumulated result; recursive call is the result
    * `foldRight` is recursive into an argument: each iteration applies `f` to the next value and the result of recursively folding the rest of the list
* buildin folds
  * xss.foldLeft \(0\) \(\(z,xs\)=&gt;z + xs.length\)
  * xss.foldLeft\(0\)\(f\) == \(0 /: xss\)\(f\) :foldLeft \(xs, z, f\) === f\( f\( f\( f\(z, a\), b\), c\)\)
  * xss.foldRight\(0\)\(f\) == \(xss :\ 0\)\(f\):foldRight\(xs, z, f\) === f\(a, f\(b, f\(c, z\)\)\)
* 😂
  * foldleft:xs:\(a,b,c\)=&gt;z+a+b+c..
  * foldright:xs\(a,b,c\)=&gt;z+..+c+b+a
* currying
  * xss.foldLeft \(z, f\)=&gt;xss.foldLeft \(z\) \(f\)
* high order function
  * `foreach`, `map`, `filter` are higher-order functions, because they take other functions as arguments
* functional programming
  * function are first class,create/passed as runtime,sometime sides effct is banned
  * first class: declared with in any scope, pass as a arguments to other functions, returns as result of functions
* object oriented programming
  * objects are first class,create/passed at runtime

#### for expression

* set comprehensions
  * {\(m,n\)\|m{0,..,10}^n{0..,10}^m&lt;=n}
* for \(x &lt;- xs\) sum = sum + x
* map or foreach and for expression $$a = b$$ 
  * for\(x&lt;-xs\) e===xs.foreach\(\(x\)=&gt;e\)
  * for \(x &lt;- xs\) yield e === xs.map \(\(x\)=&gt;e\)
* conditional for expression
  * for\(x&lt;-xs;if\(x%2!=0\)\) yield\(2\*x\)
* flatten operation and for expression

```scala
def flatten [X] (xs:List[List[X]]) : List[X] = xs match {
  case Nil   => Nil
  case y::ys => y ::: flatten(ys)
}
```

* \(for \(xs &lt;- xss; x &lt;- xs\) yield x\) == \(1 to 10\).toList
  * flatten function is same to for look version above, which  take a list of list and return a list of element.

#### option type

* Type-safe and principled approach to missing data
  * Example of option type\[Int\]: None,  Some\(5\)

```scala
//safeDivide: input m and n and return m/n, the special case is when n=0, thus, using
//option type to make sure every situation is safe.
def safeDivide (m:Int, n:Int) : Option[Int] = {
  if (n == 0) 
    None
  else
    Some (m / n)
}
```

* 😂why : key is return a "none" value when it is needed, for example, when m/n && n=0
* Java deal with this : Java will throw a exception when m/n and n==0
* when: 
  * distinguish value one same type.
    * -1 for bytes read
    * null reference for reference type: C c=new C\(\)
    * NULL pointer for pointers types: a\[-1\]
  * exceptions
* example

```scala
def index[X](xs:List[X],n:Int):Option[X]=> {
    xs match{
        case Nil=>None
        case y::_=>if n==0=>Some(y)
        case _::ys=> index(ys,n-1)
    }
}
```

* option type and null
  * option type have something or nothing\(two possibility\)
  * scala types represent nothing
    * Nil: empty list
    * None: empty option
    * None: reference to nonthing
    * Unit: return nothing, same to Java void

### worksheet

### homework

### quiz

## Lecture4

### class notes

#### Scheme loops

* with an incrementing counter argument n

```scheme
(let loop ([n 0])
  (display (string-append "hello " (number->string n) "\n"))
  (loop (+ n 1)))
```

#### Tail recursion

* 😂why need to use tail recursion?
  * stack:contain activation records\(AR\) for active calls, AKA stack frames
  * why call stack:AR pushed when a function/method call is made, AR popped when a function/method returns
  * runtime environment limit size of call stack, which will cause problem with deep recursion
  * stack overflow: c,OS,scheme, Java, Scala
* tail call optimization
  * recursive function return before next recursive function start
  * recursive call must be tail recursive
* what:the last step of method is recursive without other operation, thus is tail recursion.

#### Scala classes

* scala support both FP and OOP

```scala
//scala FP
(0 to 9).toList.partition ((x:Int) => x%3 == 0) //
```

```scala
//scala OOP
class Counter { //define a class
  var n = 0  //define a field
  def get () : Int = { val tmp = n; n = n + 1; tmp } //define a method
}

val c = new Counter  //create an object and using object to call function
c.get
c.get
```

* class parameters
  * constructor :`class C (f1:Int, val f2:Int, var f3:Int) { ... }`
    * `(f1:Int):` private val f1\(immutable\)
    * `(val f2:Int):` public val f2\(immutable\)
    * `(var f3:Int):` public var f3\(mutable\)
  * How to prove private
    * if ****other class\(not in C\) cannot call f1, c.f1 shows error message. The value have to be obtained by  get method.
  * How to prove immutable
    * if change the field to other number, c.f2=10 then c.f2=3 will shows error message
* class body
  * `class D (f1:Int)`
* companion objects
  * In JAVA

```java
class C {
  int f1;
  int m1 () { return f1; }
  static int f2; // static field
  static int m2 () { return f2; } //static function
}
```

* * In Scala

```java
class C {
  var f1:Int
  def m1 () : Int = f1
}
// this part is static field and function
object C {
  var f2:Int
  def m2 () : Int = f2
}
```

* * the object can only appear when following class, and only one instance of object declarations
* Singleton in java and scala
  * singleton is for creating only one object, and other class could use the only one object
    * private construction so that other class cannot create object of  this class, only themselves can create
    * private static field, singleton type, which is the object, this only object, static so other class can use the only object
    * public static getInstance function to new object so that other class could use class name to invoke method
  * in java

```java
public class Point{
    private static Point p;
    private int x;
    private int y;
    prviate Point(int x, int y){
     if(0 <= x && x <= 10 && 0 <= y && y <= 10){
         this.x=x;
         this.y=y;
      }
    }
    public static Point getInstance(int x, int y){
        if(p==null){
            return new Point(x, y);
        }
        return p;
    }
    //other methods..
    public void translate (int xDisp,int yDisp){
    x=x+xDisp;
    y=y+yDisp;
    }
}
```

* in scala

```scala
class Point private (private var x:Int, private var y:Int) {//private constructor and field
  def translate (xDisp:Int, yDisp:Int) : Unit = { 
    x = x + xDisp
    y = y + yDisp
  }
}

object Point {
  def apply (x:Int, y:Int) : Point = {
    if (0 <= x && x <= 10 && 0 <= y && y <= 10) {
      new Point (x, y)
    } else {
      throw new RuntimeException ("invalid params")
    }
  }
}

```

#### Algebraic data types

* algebraic data types
  * product types: tuples
  * sum types: tagged union
  * eg: option type, list type
* product type:tuple, can combine two different type of varible
  * Named for Cartesian product of sets: X \* Y = { \(x, y\) \| x ∈ X ∧ y ∈ Y }
  * case class: case class C\(x:Int,y:String\); val c= C\(5,"hello"\)
  * pattern match:

```text
val n:Int = c match { 
  case C (a, b) => a
}
```

* How compiler treat for case class?

```text
case class C (x:Int, y:String)
val c:C = C (5, "hello")
val a:Int = c.x
c.x = 6 // error: reassignment to val
```

* argument is immutable and visible, generate companion object with apply method
* * set union
    * Cartesian product of sets: X \* Y = { \(x, y\) \| x ∈ X ∧ y ∈ Y }
    * union of sets: X ∪ Y = { z \| z ∈ X ∨ z ∈ Y }
    * Coproduct or disjoint union of sets: X ⊕ Y = X ⊍ Y = { \(0, x\) \| x ∈ X } ∪ { \(1, y\) \| y ∈ Y }

```scala
trait DateSpecifier // interface
case class Absolute (year:Int,mon:Int,day:Int) extends DateSpecifier// a class implements interface
case class Relative (daysOffset:Int) extends DateSpecifier// another class implements interface

val ds = new Array[DateSpecifier] (2) //create an array
ds (0) = Absolute (2030, 0, 1) // Months are 0.11 // first is absolute type
ds (1) = Relative (-5) //second is relative type
```

* recursive types

```text
//peano natural number
trait PeanoNat
case object Zero              extends PeanoNat
case class  Succ (n:PeanoNat) extends PeanoNat
```

```scala
trait List[+X]
case object Nil                             extends List[Nothing]
case class  Cons[+X] (head:X, tail:List[X]) extends List[X]

def length [X] (xs:List[X]): Int = xs match {
  case Nil => 0
  case Cons(a,as) => 1 + length(as)
}
length (Cons (1, Cons(2, Nil)))
```

```text
trait Tree[+X]
case object Leaf                                 extends Tree[Nothing]
case class  Node[+X] (l:Tree[X], c:X, r:Tree[X]) extends Tree[X]
```

*  😂: a interface has two  kind of format. For list, there are Nil and Cons. For tree, there are leaf,which has no data stored at leave and node, which data tored in interval nodes and left node and right node are tree too 

### worksheet

### homework

### quiz

## Lecture5

### class notes

#### scope and lifetime

* scope of indentifer: region of text in which it may be used
* common rule: local variable scope is start declaration, end until the curly bracket.
* occurrence: occurrence situation in scope
  * free occurrence:
    * y=5\*x
  * binding occurrence:
    *  int y
  * bound occurrence:
    * int y; int x; x=6; y=5\*x;
* different possible chance to use variable:
  * normal variable, function parameters, function type parameterer\(return value\)
  * function name: int x= a function
  * class name
  *  and more
* forward declaration
  * shadowing allowed in c and c++

```c
// f is defined before, but defined twice after g, so g may shadowed first f.
char f (int x);

char g (int x) {
  return f (x) + f (x);
}

char f (int x) {
  if (x > 0) { 
    return g (x >> 8);
  } else {
    return 1;
  }
}
```

* shadowing:
  * shadowing is not allow in java 

```java
//x is defined but defined inside scope
public class C {
  static void f () {
    int x = 1;
    {
      int y = x + 1;
      {
        int x = y + 1;
        System.out.println ("x = " + x);
      }
    }
  }
}
$ javac C.java 
C.java:7: error: variable x is already defined in method f()
        int x = y + 1;
            ^
1 error
```

* * but field can be shadowed
  * scala is less strict than java, and shadowing occurs in REPL naturally
  * Althought in c variable can be shadowing, but int x=x+1 are undefined behavior.
* Lifetime of an area of memory: duration during which it is allocated
  * lifetime and scope: lifetime is the duration in storage\(like stack\) scope is duration in code part
* storage option
  * global\(static\):available for lifetime of program
  * AR\(stack\):available while function active
  * heap: avaible until deallocate
* lifetime problem:
  * lifetime is too short:
    * cause:read return other value
    * overwrite
    * incorrect
    * seurity problem
  * lifetime is too long:
    * use too much memory
    * too late in freeing other resources
  * Stack discipline
    1. \(call f\) allocate AR for f
    2. \(call g\) allocate AR for g
    3. \(return from g\) deallocate AR for g
    4. \(return from f\) deallocate AR for f
* multiple threads
  * each thread needs a separate call stack
  * call and return in separate threads are independent
* Heap allocation can use other patterns
  1. allocate M bytes
  2. allocate N bytes
  3. deallocate M bytes
  4. deallocate N bytes
* common problem
  * PLs with garbage collection（For Java, Scala, C\#, Python, Ruby, JS, Scheme, etc.）
    * lifetime too long \(not GCed\)
  * PLs with manual memory management \(C, C++\)
    * pointers to storage whose lifetime has ended
      * dangling pointers to an old AR
      * dangling pointers to `free`d heap memory  \(use after free\)
      * double `free`ing of heap memory
* dangling pointer problem

```c
// dangling pointer-stack: when f(1) return,free f,*p is pointer to freeed erea of y's address.
#include <stdio.h>
#include <stdlib.h>

int *f (int x) {
  int y = x;
  return &y;
}

int main (void) {
  int *p = f (1);
  printf ("*p = %d\n", *p);
  return 0;
}
```

```c
//not dangling pointer: when f(1) execute,result is allocated in heap, when function return, 
//value on heap is on there, p pointer to heap result value and print result value and return 0;
#include <stdio.h>
#include <stdlib.h>

int *f (int x) {
  int *result = (int *) malloc (sizeof (int));
  *result = x;
  return result;
}

int main (void) {
  int *p = f (1);
  printf ("*p = %d\n", *p);
  return 0;
}
```

```c
//dangling pointer-heap: result on heap is be freed before print
#include <stdio.h>
#include <stdlib.h>

int *f (int x) {
  int *result = (int *) malloc (sizeof (int));
  *result = x;
  return result;
}

int main (void) {
  int *p = f (1);
  free (p);
  printf ("*p = %d\n", *p);
  return 0;
}
```

#### closures

* runtime support for nested function 
  * nested function declarations not allowed in C
* why nested function?
  * Inner functions may be anonymous, sometimes aids clarity
  * Access variables from enclosing context
    * avoid duplicating parameters
    * requires some runtime support
  * potential problem
    * inner function have same variable name with outer function
    * scope of inner function and outer function
  * why has this problem?
    * when inner function is called, outer function still allocation, but when inner function return inner function cannot variable cannot be reached
    * when inner function are used twice, and using reference to remember that value, the first time outer function variable may be deallocated and cover by other value. This because stack deallocate after first time return.
      * solve:block copy and block release, copy g to heap to keep function variable
  * need solve:
    * inner function how to reach out function?
      * inner function block can reach outer variable, because outer function doesn't  deallocate
  * scala work well in the problem
    * because, instead of block copy, scala has closures to store, which is same to object.
  * closure
    * Closures store inner function and environment
    * Environment contains variables from enclosing scope
    * Lifetime of environment = lifetime of inner function
      * environment is allocated on the heap
    * environment has a mutable variable
      * Closure contains
        * pointer/reference to code for `inner`
        * copy of `x`
        * reference to shared `u` \(on heap\)

```scala
def outer (x:A) : B=>C = {
  var u:A = x
  def inner (y:B) : C = {
    ...use u and y...
  }
  u = u + 1
  inner
} 
```

* example

```text
val f:()=>Int = {var x= -1; () => { x = x + 1; x}}
val g:Int=>()=>Int = (y) => {var z= y; () => { z = z + 1; z}}
```

*  problem of closure function:
  * how to safely save the result? because the value diappear with the stack storage
    * in c, it stores in block, which is heap;in scala, it stores in closure
  * if a variable in outer function is mutable, inner function need to use it and change it, how to do it?
    * closure contain this variable as reference, shared with outer function and inner function.

### worksheet

### homework

### quiz

## Lecture6:midterm

### class notes

#### 

### orksheet

### homework

### quiz

## Lecture7

### class notes

#### nested class

```java
//interface has a apply function, taking t and return u
//class c has a function f, return I type, inside the function, create a I type object and
// inside I, there a function override function in I ,which is an anonymous inner class.
//it actually is  a closure and can use x and t. and then return I
interface I {
  U apply (T t); // for some types T and U
}

class C {
  I f () {
    int x = 0;
    I a = new I () { // new instance of anonymous class implementing I
      public U apply (T t) { 
        // can use x from enclosing scope, and t parameter
      }
    };
    return a;
  }
}
```

* we can bring it out

```text
interface I {
  U apply (T t); // for some types T and U
}

class D implements I {
  final int x;              
  D (int x) { this.x = x; } 
  public U apply (T t) { /* can use x in field, and t parameter */ }
}

class C {
  I f () {
    int x = 0;
    I a = new D (x); // new instance of named class implementing I
    return a;
  }
}
```

* java concurrency

```java
package java.lang;

@FunctionalInterface
public interface Runnable {
  void run ();
}

class Thread {
  Thread (Runnable target); // Allocates a new Thread object
  ...
}
...
new Thread (new Runnable () {
  public void run () { /* packaged code here */ }
}).start ();
```

* scala using java concurrency

```text
val threads : List[Thread] = {
  for (i <- (1 to 26).toList) yield {
    val ch : Byte = ('A' + i - 1).asInstanceOf[Byte]
    new Thread (new Runnable {
      def run () = {
        while (true) { System.out.write (ch) }
      }
    })
  }
}
for (t <- threads) { t.start }
```

#### L-value

* R-MODE AND L -MODE
  * R-MODE: Right-hand `x` denotes value read from storage location
  * L-MODE:Left-hand `x` denotes the storage location 
  * temporary value is not l value
* call by value
  * For `g(x)` with `x = 5`

    `x` evaluates to `5`

    a location containing `5` is given to `g`

    location is different to that of `x`!
* call by reference
  * Some PLs use call-by-reference \(CBR\)
  * To run `g (e)`
    * evaluate `e` to an l-value `r`
    * pass the l-value `r` to `g`
    * callee changes via `r` are visible to caller
* Can temporary values be passed as l-values?
  * `f(x+1)` is not obviously legitimate in CBR
* **SCALA: PASSING A FUNCTION**
  * Scala allows functions as parameters
  * A function that takes no arguments can be seen as a delayed value, also called a thunk
  * Scala has a special syntax for using thunks as parameters, Call-by-name parameters are non-strict
  * 

#### argument passing

### worksheet

### homework

### quiz

## Lecture8

### class notes

#### More scope

* in c

```text
//The second  int x=x+1 is first int x, then execute x+1,so x is not initialize in the program
int main (void) {
  int x = 10;
  {
    int x = x + 1;
    printf ("x = %08x\n", x);
  }
  return 0;
}
```

* in scala,  `val x:Int= x*1`
  * x is initialized  to 0 first when declaration, and then executing x\*1, result is 0.
* Scala stream: more complex definition, recursive definition

```scala
//worong recursive definition: when declared xs,xs is initialized as null, 
//and we use xs in right part, this time xs is still null,and not Nil,so nullpointerException
scala> val xs:List[Int] = 1 :: xs
java.lang.NullPointerException
  ... 28 elided
```

we can try by using class case version list, it is still wrong.

* Solving: try to delay evaluation of list tail, stream do it !
* Stream: consider second parameter by name.
* `#::` is non-strict in right-hand argument

```scala
//principle: using function to delay the evaluation of tail
scala> case class T(head:Int, tail:()=>T)
defined class T

scala> val ts:T = T(1, ()=>ts)
ts: T = T(1,$$Lambda$1324/2038353966@4d500865)

scala> ts.tail().tail().head
res14: Int = 1
// stream way:
scala> val ones:Stream[Int] = 1 #:: ones
ones: Stream[Int] = Stream(1, ?)

scala> ones.take (5)
res0: scala.collection.immutable.Stream[Int] = Stream(1, ?)

scala> ones.take (5).toList
res1: List[Int] = List(1, 1, 1, 1, 1)
```

* **dynamic and static scope**
* problem: 
  * inner function may have to use the variable which come from outer variable. if there are variables   using same name with outer variable,which one should the function use?
  * globle varible and local varible have same name in a function, ,when function use this name as variable, use which variable?
    * 1 possibility: using local variable in function and print global variable,beacause function is using the most recently declared variable ---- dynamic 
    * 2 possibility: using globle variable in function and print global variable,because x is bound to global x ----static

```c
//dynamic scope language print 0
//static scope language print 1
var x:Int = 0
def foo () {
  x = x + 1
}
def bar () {
  var x:Int = 0
  foo ()
}
bar ()
println (x)
```

* static scope: identifiers are bound to the **closest binding occurrence in an enclosing block of the program code.** 
* dynamic scope:identifiers are bound to **the binding occurrence in the closest activation record.**
* dynamic scope language:lisp but common lisp fixed it 
* static scope language: scheme ,scala, java, python

#### case  study:Javascript \(scope and function\)

* background summary
  * dynamically-typed: compile time no error, just happen in runtime
  * object-oriented
  * functional
  * Runs in browsers and elsewhere with [Node.js](https://nodejs.org/en/)
  * use more functional  more than object
  * evolved fast
* javascript development option: jsbin
* document object model
  * DOM tree model of HTML document
  * Browser displays runtime changes to DOM tree
  * JS introduced for manipulating DOM tree
  * Callback functions for event handling
* JS WAT\(horrible part\)
* syntax
  * print:console.log \(1 + 2\);
  * document: be displayed in the browser
  * function:

```javascript
function f (x) {
  console.log ("Called with " + x);
  return x + 1;
}

f (5);
 
```

* **javascript scope and closures**

```javascript
//the declara of a in function f is hoisted in top of function f 
var a = 1;
function f () {
  console.log ("f1: a = " + a);
  { var a = 2;
    console.log ("f2: a = " + a);
} }
function main() {
  console.log ("m1: a = " + a);
  f ();
  console.log ("m2: a = " + a);

output: 
1
undefined
2 
1
```

* javascript scope
  * hoisting: the enclosing declared a variable, the declaration will be hoisted to top of function, and initialize to "undefined", even if I have a condition and condition is false.
  * let: is  fixed this let x, is in the block it should be 
* collection processing
  * var xs = \[ 11, 21, 31 \]; 
  * xs.map \(x =&gt; \(2 \* x\)\); \[22,42,62\]
  * xs.filter \(x =&gt; x%7===0\) :\[21\]
  * xs.reduce \(\(\(z,x\) =&gt; z+x\), 0\): 0+11+21+31
* common scope problem
  * if I want both inner and outer function can change a variable, then create a closure
  * if I want the variable only can change in outer function and stop change in inner function, we can use  blcok scope: let 
    * of no let, we can use IIFE, which create a scope that local variable can only survive in inner function

```javascript
var funcs = [];
for (var i = 0; i < 5; i++) {
//the function here is for give me a scope, so that x can only survive in the scope?
  (function () { 
    var x = i;
    funcs.push (function () { return x; });
  }) ();
}
console.log (funcs[0] ()); // prints 0
```

* **javascript object oriented**
  * java OOP 

```java
//define a object in java
class employee{
    String name;
    Int id;   
}
new emplyee() 
```

* javascript is different:
  * JS is not class-based: no class definition
  * Object literals have properties \(`name`, `age`, `addr`\)
  * JSON: javascript object noation
  * javascript OOP and add field dynamically and modify exsiting field properly

```javascript
// Object literal with no contents
var empty = {};

// Object literal with three properties
var person = {
  name: "Alice",
  age: 50,
  addr: "243 S Wabash Ave"
};

```

* * keys are always Strings
  * {}: empty object and empty expression
  *  it you call the method or field is not there
    * in java: compiler error 
    * javascript
      * object even \({}\)\(empty object\): undefined
      * variable:not a object: cannot read property of "variable" of undefined
      * undefined variable: "variable" is not defined
  * enmerate properties: specical iterate syntax

```text
var person = {
  name: "Alice",
  age: 50,
  addr: "243 S Wabash Ave"
};

for (p in person) {
  console.log (p + ": " + person[p]);
}
```

* Function: no different between function and method

```javascript
//object counter with field n and fucntion get
var counter = { n: 0, get: function () { return this.n++; } };
// n is in inner function,so have specify this
var counter = { n: 0, get () { return this.n++; } };
```

* encapsulation for object
  * Encapsulation via private fields/methods for PLs
  * JS properties cannot be declared private
    * no classes
    * which code could access "private" properties?
      * inner function local variable 

```javascript
//return an object with a function
function createCounter () {
  var n = 0;
  return {
    get: function () { return n++; } // n++ NOT this.n++
  };
};

var counter = createCounter ();
counter.get ();
```

```scala
//scala version just return a function, extreact function from object
def createCounter () : () => Int = {
  var n = 0
  () => { val retVal = n; n = n + 1; retVal }
}

val counter = createCounter () 
counter ()
```

* jquery is a library for javascript
*  continue javascript OOD
* delegation/prototype based inheritance
  * problem
    *  employee application, how do they diff?diff name, salary. how do they same? same function, increaseSalary\(\), change\(\)
    * original class
      * Attribute:separate
      * function: share
        * function create a local variable,varible and function share the memory, but when new a object, it will start over
      * new objects have separate place to store field and a link to function, and a shared place to store function code
    * but javascript, they don"t have class
    * solving: delegation
      *  embedding class as object, and store method in here, a pointer called prototype
  * this
    * JS binds `this` based on calling context. 
      * `o.m()` is method context, `this===o`
      * `f()` is function context, `this===global/window`
      * `new C()` is constructor context, `this` is new object
  * function is  method in javascript
    * problem 2
      * function in object is a closure,  closures aren't bind "this", this is bound to the globle object, but scala different

```text
//javascript return a function, which create a closure, the closure is not bind this,
//so the closure is not bind o
var o = {
  v : 5,
  add : function (xs) {
    return xs.map(function(x){ return this.v + x; });
  }
}
o.add([10,20,30]) // [ NaN, NaN, NaN ]
```

* * solution:

```text
var o = {
  v : 5,
  add : function (xs) {
    return xs.map(function(x){ return this.v + x; }.bind(this));
  }
}
o.add([10,20,30]) // [ 15, 25, 35 ]
```

* js view properties
* js object is separate, function are shared
  * js object cointains propertie of the object and the properties of prototype,so that fucntion can be share
  * 

### homework

### quiz

## Lecture9

### class notes

#### dynamic dispatch

* encapsulation
  * private: object in this class can see
* direct calls:refer to callee directly by name
  * indirect call:C allows indirection to code via function pointers
  * diff: in assembly: direct just name where we will going; indirect we put  the place in the register, and then we jump to the value stored in register
* example of indirect call

```text
//indirect calls:A type of array should bind with A function,
// but it return its actual time of function, so it is dynamic dispatch
class A           { void foo () { System.out.println ("A"); } }
class B extends A { void foo () { System.out.println ("B"); } }
class C extends A { void foo () { System.out.println ("C"); 

public class Driver {
  public static void main (String[] args) { 
    A[] as = { new B(); new C (); }
    for (A x : as)
      x.foo ();
  }
}

```

* 😂:dynamic dispatch: object and function\(\) binding together; if it occur in runtime, it is dynamic dispatch; if it occur in compile time, it is static binding
* Java / Scala anon. functions use dynamic dispatch,Implementation of dynamic dispatch in C++ soon

#### subtyping

* interface=contact
  * Informal usage for dynamically-typed PLs

    * documentation describes what is allowed
    * dynamic errors may occur if the interface is not obeyed or the interface was incorrect
      * dynamic typed PL, they only talk about what field they has, properties they have 
      *  if you don't have this properties, the dynamic error occur

    Formal usage for statically-typed PLs

    * static checks that data uses interfaces correctly
    * to eliminate dynamic errors
    *  These slides apply to statically-typed PL
* focus on static typed language
  * subtyping :c have all the properties of B \(canry and bird\)
    * C&lt;:B
    * scala: from nothing to any
  * subtyping and substitution
    * upcasting
      * If `Y<:X` and `e:Y`, then `e:X` also
        * \(because `e` satisfies the contract for type `X`\)
        * if someone wants a bird, he is happy to get a canry
        * This substitution is sometimes called upcasting
    * downcasting
      * Downcasting is potentially unsafe
        * requires explicit cast in Java, Scala, etc.
        * also requires storing type at runtime
        * downcast introduces a dynamic check \(Java\)
      * subtyping preorder
        * Subtyping relation `<:` is a preorder on types
          * reflexive - `X<:X`
          * transitive - if `X<:Y` and `Y<:Z` then `X<:Z`
    * `PL`
      * `top`
        * Some PLs have a `Top` type

          * `X<:Top` \(greater than all other types\)

          In Java - `java.lang.Object` above reference types

          In Scala - `scala.Any` above all types

        * In Scala - `scala.AnyRef` above reference types
      * bottom
        * Most PLs do not have a `Bottom` type

          * `Bottom<:X` \(less than all other types\)

          Recall [Scala type hierarchy](http://fpl.cs.depaul.edu/jriely/courses/csc447/assets/images/ScalaClassHierarchy_New.png)

          In Scala - `Bottom` is `scala.Nothing`

          Important for typing uses of `Nil`

          * `Nil:List[Nothing]`
          * `List[Nothing]<:List[X]`
    * List
      * Subtyping for Scala lists: If `Y<:X` then `List[Y]<:List[X]`
    * **COVARIANCE:referent to same thing, but have different type parameter**
      * `List` said to be covariant， arrays is not covariant

        Generally, `C[-]` is covariant if and only if

        * `Y<:X` implies `C[Y]<:C[X]`
    * invariant
      * scala array invariant

```scala
class B {
  def f (x:Int) : Int             = 1 
}

class C extends B {
  override def f (x:Int) : Int    = 2
  def g () : Int                  = 3
}

val xs1:Array[B] = Array (new B, new B) // OK
val xs2:Array[C] = Array (new C, new C) // OK
val xs3:Array[B] = Array (new B, new C) // OK, because C <: B
val xs4:Array[B] = xs2 // Not allowed; following steps must not occur
xs4 (0) = new B        // OK, because xs4:Array[B]
xs2 (0).g ()           // OK because xs2 (0) : C (statically)
                       // but fails because xs2 (0) : B (dynamically)
```

cannot have two different reference to same array

* scala not allow upcasting and downcasting
* java  array convariant

```java
public class Driver {
  public static void main (String[] args) {
    C[] xs2 = new C[] { new C (), new C () };
    B[] xs4 = xs2;     // Uses covariance of Java arrays
    xs4[0] = new B (); // Compiles OK, fails at runtime
    xs2[0].g (new String[] { }); 
  }
}
```

* java allow upcastign and downcasting, not allow change

### worksheet

### homework

### quiz

## Lecture10

### class notes

#### inheritance

* subtyping:  one type can be used as different type,  bird is a canary
* inheritance: reuse  the code
* delegation id different from inheritance 

#### case study: c++ and vtable

* OO feather
  * object heap allocation:Counter C
  * object stack allocation:Cunter \*p=new Counter\(\);
* inheritance
  * fields
* dispatch
  * default: static dispatch
  * dynamic dispatch: virtual



### worksheet

### homework

### quiz

## Scala method

* List method

```scala
// isEmpty:input a list return boolean, spliting to two situation
// if list has element return true, else return false
def isEmpty [X] (xs:List[X]) : Boolean = xs match {
  case Nil   => true
  case y::ys => false
}
```

```scala
//head: input a list return a element of list(head)
//return first element of list, else throw exception
def head [X] (xs:List[X]) : X = xs match {
  case Nil   => throw new NoSuchElementException ()
  case y::ys => y
}
```

```scala
//tail:input a list return a list(without head)
//return all element without head, else throw a exception
def tail [X] (xs:List[X]) : List[X] = xs match {
  case Nil   => throw new NoSuchElementException ()
  case y::ys => ys
}
```

```scala
//append:intput two list with same type element,return one list
//if list a has element, append that element with new list recursively,
//if list a has no element, append list b to new list
def append [X] (xs:List[X], ys:List[X]) : List[X] = xs match {
  case Nil   => ys
  case z::zs => z::(append (zs, ys))
}
```

```scala
//foreach:input a list with any type and a function take list element and return unit
//take first element into f function and recursively do this
def foreach [X] (xs:List[X], f:X=>Unit) : Unit = {
  xs match {
    case Nil   => ()
    case y::ys => { 
      f (y)
      foreach (ys, f)
    }
  }
}
```

```scala
// sum: input a list of Int and return a sum of them
//using a variable inside sum, and for each function, sum every element in the list with result and print result
def sum (xs:List[Int]) : Int = {
  var result = 0
  xs.foreach ((x:Int) => result = result + x)
  result
}
```

```scala
// transform: input a list of someting and resturn a list of otherthing.
//take the first element and transform it and then recursively do that
def transform (xs:List[Int]) : List[String] = {
  xs match {
    case Nil   => Nil
    case y::ys => ("%02d".format (y)) :: transform (ys)
  }
}
```

```scala
//map: input a list of X and return a list of Y
//take the first element into f function and recursively do that,
//which is map the element to f function
def map [X,Y] (xs:List[X], f:X=>Y) : List[Y] = {
  xs match {
    case Nil   => Nil
    case y::ys => f (y) :: map (ys, f)
  }
}
```

```scala
//identity: input a list of X return same list of X
//combine y with recursive function
def identity [X] (xs:List[X]) : List[X] = {
  xs match {
    case Nil   => Nil
    case y::ys => y :: identity (ys)
  }
}
```

```scala
//filter: input a list X return a filted list
//take y to conditional argument if true return, else delete y and recursively argue next y
def filter [X] (xs:List[X], f:X=>Boolean) : List[X] = {
  xs match {
    case Nil            => Nil
    case y::ys if f (y) => y :: filter (ys, f)
    case _::ys          => filter (ys, f)
  }
}
```

```scala
//foldleft:input a list, a accumulator and a function return a number
//execute f first and then recursion: z,f,foldleft,f,foldleft
//the recursion occur in the last step of function, parameter change as a recusion
//it don't use too much stack memory
def foldLeft [Z,X] (xs:List[X], z:Z, f:((Z,X)=>Z)) : Z =  xs match {
  case Nil   => z
  case y::ys => foldLeft (ys, f(z,y), f)
}
val xss = List(List(1,2,3),List(4,5,6,7),List(8,9),List(10))
foldLeft (xss, 0, (z:Int,xs:List[Int])=>z + xs.length)
```

```scala
//foldright:input a list,a accumulator and a fucntion return a number
//execute recursion first then f function: foldright,foldright,foldright...,z,f,f,f,f...
//the recusion occur before the f executing, it will use stack everytime recursion execute
def foldRight [X,Z] (xs:List[X], z:Z, f:((X,Z)=>Z)) : Z =  xs match {
  case Nil   => z
  case y::ys => f (y, foldRight (ys, z, f))
}
val xss = List(List(1,2,3),List(4,5,6,7),List(8,9),List(10))
foldRight (xss, 0, (xs:List[Int],z:Int)=>xs.length + z)
```

```scala
//flatten: input a list of list and return a list of element
//using concatenate notation to connect element and list
def flatten [X] (xs:List[List[X]]) : List[X] = xs match {
  case Nil   => Nil
  case y::ys => y ::: flatten(ys)
}
```

```scala
//flatMap: input a List of X and a function, which change X to a list and return list
//pass a list of element to f function and return a list and combine with other list
def flatMap [X,Y] (xs:List[X], f:X=>List[Y]) : List[Y] = {
  xs match {
    case Nil   => Nil
    case y::ys => f (y) ::: flatMap (ys, f)
  }
}
```

