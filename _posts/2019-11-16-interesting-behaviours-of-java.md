---
layout: post
title: Interesting Behaviours of Java
image: java-island.jpg
categories: [java]
---

There is [a famous video](https://www.destroyallsoftware.com/talks/wat) that showcases some weird "features" of JavaScript. If you haven't seen it, I highly recommend to check it out. It's hilarious (and a bit frustrating at the same time).

Java is a much stricter language than JavaScript, but there are still some, let's say "not-so-obvious", behaviours you can encounter in Java.
So let's get to it.

### Negative + negative = positive?
Let us start with an easy one:
```java
int negativeNumber = -2147483648;
int absoluteValue = Math.abs(negativeNumber);
System.out.println(absoluteValue > 0); // false.
```
In fact, `absoluteValue` is equal to `negativeNumber`. `negativeNumber` is equal to `-2^31`, which is the smallest possible number to represent in an `int`. When you negate that, you get `2^31`. However, the largest possible number that an `int ` can represent is `2^31-1`, therefore  `2^31` will roll over to `-2^31`. The reason for that is that most computers use the [Two's Complement](https://en.wikipedia.org/wiki/Two%27s_complement) to represent numbers.

### Integer vs. int - easy
This one is also not too surprising:
```java
Integer a = new Integer(1);
Integer b = new Integer(1);
Integer c = 1;
System.out.println(a >= b); // true
System.out.println(a <= b); // true
System.out.println(a == b); // false
System.out.println(a == c); // false
```
Because there is no `<=` or `>=` operator, the compiler auto un-boxes the `Integer`s to primitive `int`s and the comparison works as expected.
The `==` operator exists on `Integer` though (inherited from `Object`), compares the two integers by reference and therefore return false.
`c` is automatically boxed to `Integer`, therefore the previous statement applies.

### Wait, not so easy after all?
This next one is a bit more confusing at first:
```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b); // true
Integer c = 128;
Integer d = 128;
System.out.println(c == d); // false
Integer e = 128;
int f = 128;
System.out.println(e == f); // true
int g = 128;
int h = 128;
System.out.println(e == f); // true
```
The JVM caches all `Integer` values from -128 to 127 at the start, therefore `a` and `b` have the same reference.

### 🧙 Casting Integers
```java
Object o = (Integer) null; // ok (redundant cast)
int i = (Integer) null; // nullpointer exception
```
The cast itself is successful, but the auto un-boxing tries to call `.intValue()`, which obviously throws a nullpointer exception.


### The ternary operator as a shorthand for if-else
```java
Object a = true ? new Integer(1) : new Double(2.0);
Object b;
if (true) b = new Integer(1); 
else b = new Double(2.0);
System.out.println(a); // 1.0
System.out.println(b); // 1
```
The ternary operator has one type only! Therefore it is in fact _not_ an exact alternative to if-else

### Variable names in Java
We all know the rules of varibale names in Java. They can start with a letter, _ oder $. Also the names must be unique in the same scope. But guess what:
```java
String _‎ = "This";
String _‏ = "actually";
String _‎‏ = "compiles!";
System.out.println(_‎+_‏+_‎‏); // This actually compiles!
```
Wait, what?! But Java doesn't allow redeclaration of a variable! True. I tricked you. But Java _does_ allow hidden characters in variable names ;) I used `\u200e` and `\u200f` here.
