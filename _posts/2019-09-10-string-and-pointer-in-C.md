---
layout: post
current: post
cover: assets/images/cover-img/c20.jpg
navigation: True
title: Strings and Pointers in C
date: 2019-09-10
comments: true
---

**This is a self-reflection on Pointers in C, especially its use in String**

-------------------------------------

For those who have been heavily using high-level programming languages, the use of pointers in C is a bit confusing. In Java, for example, we do have pointers(we call reference) but is built under the cover. JVM handles pointers for us. In C, however, we need to handle pointers ourselves. 

**A pointer is an explicit memory address**

For example,

i is an integer variable in memory, located at, say, address 1056

p is a variable that “points to” an integer, located at, say, address 2004


<img src="assets/images/Ptr/Ptr-1.jpg" alt="Ptr-1" style="width: 40%;">

```c
int i = 10;       // i is type int, and its value is initiated as 10

int *p = &i;      // this is called referencing i. We takes is address and assign it to a pointer p. p is type pointer  

*p = 5;           // this is called dereferencing p. We goes to the memory address pointed by p and change its value to

```

Having the basic understanding of pointers, I want to look at its use in Strings. The reason why I choose String is that in C we don't really have Strings object. All we have is a char array with a terminated null character to represent a string. For example, "hello" in C is actually a char array of length 6, ```['h', 'e', 'l', 'l', 'o', '\0']```, where ```'\0'``` is the terminated null.

```c
// First of all, there are several ways to create a string, aka char array

1. char str[] = "Hello";                       // we know exact what the string is 

2. char str[50] = "Hello";                     // we know what the string is but can also allocate a size

3. char str[] = {'H','e','l','l','o','\0'};    // use actuall char array

4. char str[6] = {'H','e','l','l','o','\0'};   

// Let's now look at some examples of pointers in string

char str1[] = "Hello";
printf("%s", st1);          // Hello
printf("%c", str1[0]);      // H
printf("%c", *str1)         // H

char *str2[] = {"Hello", "World"};
printf("%s", st2[0]);       // Hello
printf("%c", *st2[0]);      // H
```

The main takeaway here is that str2 is actually an array of size 2, containing 2 addresses, first one for "Hello" and second one for "World". 