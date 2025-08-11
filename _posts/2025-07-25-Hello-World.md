---
title: "Hello World"
date: 2025-07-26 00:00:00 +0000
categories: [Coding]
tags: [Reverse engineering, C]
---

My first C program.
Coding a Hello World Program in C and reversing it


## Coding

![code pic](assets/img/blogposts/helloworld1.png)

There are a total of 5 lines in this code and I took time to understand each.

### Header files 

``` #include <stdio.h> ```
is a line that imports the stdio.h library to the code 

as it allows us to use printf. stdio stands for STanDard Input Output, and the h stands for header as it is a header file.

### Function 

``` int main(void) ``` defines the function and the function type.

### Print 

``` printf ``` , which stands for print format allows us to print our text to the terminal. 

### Text

``` "Hello   World!" ``` is the text being printed out by the printf statement and the ``` \n ``` simply means new line.

## Reversing

To reverse my hello world program, I loaded the exe file in IDA both with and without debug symbols. and spent a long time basically trying to understand the disassembly. 

![code pic](assets/img/blogposts/helloworld2.png)


Learnt how to identify the function entry point since apparently IDA automatically takes you to start which is the general entry point for [crt startup](https://stackoverflow.com/a/22935162) but not necessarily the main function.

Was unfortunately still not able to find the main function when I opened without symbols, should work on that.

Was easier with the symbols and I spent some time altering the code however I could to see what would change. 

Might have to write an article for setting up visual studio since that was a huge challenge for me.