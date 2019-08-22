---
layout: post
current: post
cover: assets/images/cover-img/c18.jpg
navigation: True
title: The Categorization of Programming Languages 
date: 2019-08-20
comments: true
---


**This post is a basic introcuction of many programming languages, ans their class. For example, what is scripting languages, what prgramming languages are in this class, how does it differ from other classses, say functional languages. Funtional languages part is a note from <a src="http://blog.jenkster.com/2015/12/what-is-functional-programming.html">Kris Jenkins</a>**

------------------

I personally had hard times understanding why I am using a particualr programming language over the other. Just knowing the syntax of one language or how to write a program doesn't mean that you fully understand why you are using it. For a long time, I started with the languages taught in school and kept writing codes without even understanding the background of it. There are tons of programming languages and more are emerging these days. Thus, knowing their properties is rather important.

Before I dive into languages themselves, I want to first differentiate what is **compilers** and what is **interpreters**. Basically, compiliers and interpreters are two different ways to execute your program.

- **Compiler**: A compiler takes the entire program(bunch of codes) and convert it into binary code which can be executed by the machine. You should already know that computers only understand binary digits.

- **Interpreter**: An interpreter directly executes the pragram withouy having it previously converted. That being said, interpreter only runs when needed. 

They are different in many details but probably the most obvious one is that once a program is compiled, its source code is not useful for running the code. For interpreted programs, the source code is needed to run the program every time.


<br><br>
Now since we understand compilers and interpreters, we can start to dive into some language families.

**Aessembly Languages**

As I mentioned above, a machine(computer) only understands machine language, i.e. binary bits. Assembly is the one level up language, that being said, it is the medium from higer-level languages(any languages higher than assemly) to the machine language. Assembly code is converted into executable machine code by a utility program referred to as an assembler.

<br><br>
**Scripting Languages**

Scripting language is a category of programming languages. The most important feature of such languages is that, unlike some other languages that is typically compiled first before being allowed to run, scritping languages are interpreted from soure code one command at a time.

> Some common scripting languages include: Python, JavaScript, PHP, Ruby, Node.js, etc.

Indeed, if you have used Python(or Jupyter notebook), you must know that we can run the single cell at a time, instead of having all our programs written out and run once for all. 


<br><br>
**Functional Languages**

Before I give the definition of what is functional languages, let's first look at two blocks of code:

```java
public int square(int x){
    return x*x;
}
``` 
This is a very simple function that takes an input, an integer, and outputs its sqaure value. So we have one input and one output.

Let's look at another function:
```java
public void processNext() {
    Message message = InboxQueue.popMessage();

    if (message != null) {
        process(message);
    }
}
```
You might be thinking, well, this function does not take any inputs nor outputs anything. The fact is, it has an hidden inputs and hidden outputs. The hidden input is the state of the ```InboxQueue``` before the ```popMessage()``` call, and the hidden outputs are whatever ```process()``` causes, plus the state of ```InboxQueue``` after we’re done. That being said, ```processNext()``` requires things, and causes things, but you could never guess what just by looking at the API.

Functional programming is about writing pure functions, about removing hidden inputs and outputs as far as we can, so that as much of our code as possible just describes a relationship between inputs and outputs. A functional programming language is one that supports and encourages programming without such hidden states.

> Some common scripting languages include: Scala, Erlang, Clojure, etc.



<br><br>
**Other high-level languages**

The rest in the languages family are high level languages. There are some that you might be familiar with such as Java, Go, and many others.

Unlike scripting languages, which doesn't need compiler to compile the code, these languages are going to be compiled first to be able to work. For example, java code needs to be compiled by java compiler ```javac```, C code needs to be compiled by C compiler ```gcc```, and interestingly, Google Chrome has a special engine for compiling code written in JavaScript called V8.