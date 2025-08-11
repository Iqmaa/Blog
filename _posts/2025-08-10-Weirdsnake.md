---
title: "Weirdsnake"
date: 2025-08-10 00:00:00 +0000
categories: [CTF]
tags: [Reverse engineering, python, CTF]
---

Weirdsnake is a medium level reverse engineering CTF from pico - [link](https://play.picoctf.org/practice/challenge/428?assigned=0&category=3&page=1&retired=0&search=snake&solved=0)

## Challenge

![CTF question](assets/img/blogposts/weirdsnake1.png)

After downloading the file, which turned out to be python bytecode which I was completely unfamiliar with. I read a couple of [articles](https://dev.to/emminex/python-bytecode-a-beginners-guide-35bb) that explained bytecode and helped me to understand it.

Python bytecode is the lower level representation of your python code, translated by the interpreter for efficiency.

So now, translating the bytcode into actual python code. Here's the general syntax for the code.

```line number  |  offset  |  instruction  |  arg  |  (value)```

* ```line number``` which corresponds to line number of the original code
* ```offset``` it's offset to the original binary code
* ```instruction``` the operation to be executed
* ```arg``` the argument to be passed to the instruction
* ```value``` the value e.g variable name 

### Line 1

~~~
  1           0 LOAD_CONST               0 (4)
              2 LOAD_CONST               1 (54)
              4 LOAD_CONST               2 (41)
              6 LOAD_CONST               3 (0)
              8 LOAD_CONST               4 (112)
             10 LOAD_CONST               5 (32)
             12 LOAD_CONST               6 (25)
             14 LOAD_CONST               7 (49)
             16 LOAD_CONST               8 (33)
             18 LOAD_CONST               9 (3)
             20 LOAD_CONST               3 (0)
             22 LOAD_CONST               3 (0)
             24 LOAD_CONST              10 (57)
             26 LOAD_CONST               5 (32)
             28 LOAD_CONST              11 (108)
             30 LOAD_CONST              12 (23)
             32 LOAD_CONST              13 (48)
             34 LOAD_CONST               0 (4)
             36 LOAD_CONST              14 (9)
             38 LOAD_CONST              15 (70)
             40 LOAD_CONST              16 (7)
             42 LOAD_CONST              17 (110)
             44 LOAD_CONST              18 (36)
             46 LOAD_CONST              19 (8)
             48 LOAD_CONST              11 (108)
             50 LOAD_CONST              16 (7)
             52 LOAD_CONST               7 (49)
             54 LOAD_CONST              20 (10)
             56 LOAD_CONST               0 (4)
             58 LOAD_CONST              21 (86)
             60 LOAD_CONST              22 (43)
             62 LOAD_CONST              23 (104)
             64 LOAD_CONST              24 (44)
             66 LOAD_CONST              25 (91)
             68 LOAD_CONST              16 (7)
             70 LOAD_CONST              26 (18)
             72 LOAD_CONST              27 (106)
             74 LOAD_CONST              28 (124)
             76 LOAD_CONST              29 (89)
             78 LOAD_CONST              30 (78)
             80 BUILD_LIST              40
             82 STORE_NAME               0 (input_list)
~~~

This is the first segment of the bytecode, which represents line number 1 of the original code, and it defines a list called input_list with 40 values ``` 4, 4, 41, 0, 112, 32, 25, 49, 33, 3, 0, 0, 57, 32, 108, 23, 48, 4, 9, 70, 7, 110, 36, 8, 108, 7, 49, 10, 4, 86, 43, 108, 122, 14, 2, 71, 62, 115, 88, 78```

Converting this to python code we have

~~~python
input_list = [4, 54, 41, 0, 112, 32, 25, 49, 33, 3, 0, 0, 57, 32, 108, 23, 48, 4, 9, 70, 7, 110, 36, 8, 108, 7, 49, 10, 4, 86, 43, 108, 122, 14, 2, 71, 62, 115, 88, 78] 
~~~

### Line 2

The second segment of the bytecode

~~~
  2          84 LOAD_CONST              31 ('J')
             86 STORE_NAME               1 (key_str)

  3          88 LOAD_CONST              32 ('_')
             90 LOAD_NAME                1 (key_str)
             92 BINARY_ADD
             94 STORE_NAME               1 (key_str)

  4          96 LOAD_NAME                1 (key_str)
             98 LOAD_CONST              33 ('o')
            100 BINARY_ADD
            102 STORE_NAME               1 (key_str)

  5         104 LOAD_NAME                1 (key_str)
            106 LOAD_CONST              34 ('3')
            108 BINARY_ADD
            110 STORE_NAME               1 (key_str)

  6         112 LOAD_CONST              35 ('t')
            114 LOAD_NAME                1 (key_str)
            116 BINARY_ADD
            118 STORE_NAME               1 (key_str)
~~~

This segment defines a variable key_str with the values ```t_Jo3``` in that particular order (really important to note).

Rewriting it in python, it looks something like this

~~~python
key_str = 'J'
key_str = '_' + key_str
key_str = key_str + 'o'
key_str = key_str + '3'
key_str = 't' + key_str
~~~

which can be shortened to one line

~~~python
key_str = "t_Jo3"
~~~

### line 3

~~~

  9         120 LOAD_CONST              36 (<code object <listcomp> at 0x7f5ef6665d40, file "snake.py", line 9>)
            122 LOAD_CONST              37 ('<listcomp>')
            124 MAKE_FUNCTION            0
            126 LOAD_NAME                1 (key_str)
            128 GET_ITER
            130 CALL_FUNCTION            1
            132 STORE_NAME               2 (key_list)

...

Disassembly of <code object <listcomp> at 0x7f5ef6665d40, file "snake.py", line 9>:
  9           0 BUILD_LIST               0
              2 LOAD_FAST                0 (.0)
        >>    4 FOR_ITER                12 (to 18)
              6 STORE_FAST               1 (char)
              8 LOAD_GLOBAL              0 (ord)
             10 LOAD_FAST                1 (char)
             12 CALL_FUNCTION            1
             14 LIST_APPEND              2
             16 JUMP_ABSOLUTE            4
        >>   18 RETURN_VALUE
~~~

Analysing this segment of code, we see that it takes the characters in the ```key_str``` list and puts its ASCII equivalent in a list ```key_list```. 

Rewriting this in python we have

~~~python
key_list = [ord(char) for char in key_str]
~~~

### Line 4 & 5

~~~
 11     >>  134 LOAD_NAME                3 (len)
            136 LOAD_NAME                2 (key_list)
            138 CALL_FUNCTION            1
            140 LOAD_NAME                3 (len)
            142 LOAD_NAME                0 (input_list)
            144 CALL_FUNCTION            1
            146 COMPARE_OP               0 (<)
            148 POP_JUMP_IF_FALSE      162

 12         150 LOAD_NAME                2 (key_list)
            152 LOAD_METHOD              4 (extend)
            154 LOAD_NAME                2 (key_list)
            156 CALL_METHOD              1
            158 POP_TOP
            160 JUMP_ABSOLUTE          134
~~~

I was initially confused if this segment was a while loop or an if-else statement. It turned out to be a while loop proved by the  ``` 160 JUMP_ABSOLUTE          134``` line that ensures at the end it jumps back to the beginning of the function. 

An if-else would have just continued on to the next line.

~~~python
while len(key_list) < len(input_list):
   key_list.extend(key_list)
~~~

### Line 6

~~~
 15     >>  162 LOAD_CONST              38 (<code object <listcomp> at 0x7ff3b9776df0, file "snake.py", line 15>)
            164 LOAD_CONST              37 ('<listcomp>')
            166 MAKE_FUNCTION            0
            168 LOAD_NAME                5 (zip)
            170 LOAD_NAME                0 (input_list)
            172 LOAD_NAME                2 (key_list)
            174 CALL_FUNCTION            2
            176 GET_ITER
            178 CALL_FUNCTION            1
            180 STORE_NAME               6 (result)
        
Disassembly of <code object <listcomp> at 0x7ff3b9776df0, file "snake.py", line 15>:
 15           0 BUILD_LIST               0
              2 LOAD_FAST                0 (.0)
        >>    4 FOR_ITER                16 (to 22)
              6 UNPACK_SEQUENCE          2
              8 STORE_FAST               1 (a)
             10 STORE_FAST               2 (b)
             12 LOAD_FAST                1 (a)
             14 LOAD_FAST                2 (b)
             16 BINARY_XOR
             18 LIST_APPEND              2
             20 JUMP_ABSOLUTE            4
        >>   22 RETURN_VALUE
~~~

This segment of bytecode pairs up element from both the ```input_list``` and ```key_list```, stores them as variables ```a``` and ```b```, then performs a binary XOR operation on them and stores the results in a new list named ```result```

Our resulting python code looks something like this.

~~~python
result = [a^b for a,b in zip(input_list, key_list)]
~~~

### Line 7

This is the last code segment

~~~
 18         182 LOAD_CONST              39 ('')
            184 LOAD_METHOD              7 (join)
            186 LOAD_NAME                8 (map)
            188 LOAD_NAME                9 (chr)
            190 LOAD_NAME                6 (result)
            192 CALL_FUNCTION            2
            194 CALL_METHOD              1
            196 STORE_NAME              10 (result_text)
            198 LOAD_CONST              40 (None)
            200 RETURN_VALUE
~~~

This segment of code maps the ASCII values from the ```result``` list we ceated in a previous line of code(line 3) back to their character value, 'joins' them, and saves the resulting string as ```result_text```

It's corresponding python code is

~~~python
result_text = ''.join(map(chr, result))
~~~

### Line 8

We add a final line of our own to the code to read the results from the previous lines. 

A simple print will do. 

~~~python
print(result_text)
~~~

### Final code

The complete code looks like this.

~~~python
input_list = [4, 54, 41, 0, 112, 32, 25, 49, 33, 3, 0, 0, 57, 32, 108, 23, 48, 4, 9, 70, 7, 110, 36, 8, 108, 7, 49, 10, 4, 86, 43, 108, 122, 14, 2, 71, 62, 115, 88, 78] 

key_str = "t_Jo3"

key_list = [ord(char) for char in key_str]

while len(key_list) < len(input_list):
   key_list.extend(key_list)

result = [a^b for a,b in zip(input_list, key_list)]

result_text = ''.join(map(chr, result))

print(result_text)
~~~

After running the code I got the flag.

![flag](/assets/img/blogposts/weirdsnake2.png)