---
title: "Calculator"
date: 2025-08-17 00:00:00 +0000
categories: [Coding]
tags: [Reverse engineering, C]
---

Coding and Reversing a *very* basic calculator in C.

## Coding

![calculator code](assets/img/blogposts/calc.png)

### Line 1-2

```c
#include <stdio.h>
#include <stdlib.h>
```

These are the heaader files where I imported the standard libraries I would be needing in this project.

### Line 3-6

```c
char op
int op1
int op2
int result
```

Here, I declared all the variables I would be needing in this project

```op``` is the operation to be performed.

```op1``` is the first operand, and is an integer.

```op2``` is the second operand, and is also an integer.

and ```result``` is the final integer gotten after the operation has been performed on the given operands.

### Line 9

```c
int main() {
```
This signifies the start of the main function

### Line 10-16

```c
printf("Welcome to my calculator programme\n");
printf("Select an operation");
scanf_s("%c", &op);
printf("Select the first operand; ");
scanf_s("%d", &op1);
printf("Select the second operand; ");
scanf_s("%d", &op2);
```

This segment of code prints out 
```
Welcome to my calculator programme
select an operation
```

and then takes in the input before prompting the user to type in their first and second operands, which are the numbers they want to perform the opeartion on.

### Line 18-30


```c
if (op == '-'){
    result = op1 - op2;
    printf("result is %d ", result);
}
else if (op == '+'){
    result = op1 + op2;
    printf("result is %d ", result);
}
else{
    printf("invalid input");
}
return 0;

}
```
This segment represents a conditional statement, if - else if - else.

It checks if the input matches either the subtractioon or addition operation respectively and performs the corresponding operation on the operands if it does. Otherwise, it prints out an error message ```invalid input```.

Lines 28 - 30(12-14 here) ends the programme and signifies the closing of the main function.

## Reversing

The next thing I did was to open up the code in IDA to see how it looked in assembly.

![calculator disassembly](assets/img/blogposts/recalc.png)

This is the first part of the disassembly of the main function

### Segment 1

```
; int main(...)
main proc near
```

This segment signifies the start of the main function and corresponds to [line 9](#line-9) in the C code above.

### Segment 2

```
push    rbp
push    rdi
sub     rsp, 0E8h
lea     rbp, [rsp+20h]
lea     rcx, __6E6E7DB9_calc@c ; JMC_flag
call    j___CheckForDebuggerJustMyCode
nop
```

This part is the compiler setting up the stack frame by adjusting the stack pointer(rsp) and saving important register values to it(rbp , rdi) 
### Segment 4

```
lea     rcx, _Format    ; "welcome to my calculator programme\n"
call    j_printf
nop
lea     rcx, aSelectAnOperat ; "select an operation"
call    j_printf
nop
lea     rdx, op
lea     rcx, aC         ; "%c"
call    j_scanf_s
nop
lea     rcx, aSelectTheFirst ; "select the first operand; "
call    j_printf
nop
lea     rdx, op1
lea     rcx, aD         ; "%d"
call    j_scanf_s
nop
lea     rcx, aSelectTheSecon ; "select the second operand; "
call    j_printf
nop
lea     rdx, op2
lea     rcx, aD         ; "%d"
call    j_scanf_s
nop
```

This segment corresponds to [lines 10 - 16](#line-10-16) above which prompts the user to input the operator and operands and takes in their input. 

![calculator disassembly 2](assets/img/blogposts/recalc2.png)

This is the second part of the disassembly of the main function

### Segment 5

```
movsx   eax, cs:op
cmp     eax, 2Dh ; '-'
jnz     short loc_140011933
```

This corresponds to [Line 18](#line-18-30) above. It is the start of the next part of the code which is the if statement.
It compares the user input to '-' and if user input is '-' it moves to the left branch as shown in the graph view of the disassembly above, otherwise it moves to the right signified by ```loc_140011933```.


### Segment 6

```
loc_140011933:
movsx   eax, cs:op
cmp     eax, 2Bh ; '+'
jnz     short loc_14001196A
```
This corresponds to [Line 22](#line-18-30) in the C code. It goes on to compare user input to '+', if this is valid it branches to the next segment on the left, other wise it branches to the right. 

### Segment 7

```
mov     eax, cs:op2
mov     ecx, cs:op1
add     ecx, eax
mov     eax, ecx
mov     cs:result, eax
mov     edx, cs:result
lea     rcx, aResultIsD ; "result is %d "
call    j_printf
nop
jmp     short loc_140011977
```

This corresponds to [Lines 23-25 ](#line-18-30). It is the segment the code moves to if user input is truly '+'. It performs the addition operation and prints out the result.
### Segment 8

```
loc_14001196A:
lea     rcx, aInvalidInput ; "invalid input"
call    j_printf
nop
```
This corresponds to [Lines 26-28](#line-18-30). It is the segment the code jumps to after comparing user input to both '+' and '-' and coming out false, it proceeds to print out the error message 'invalid input'. 

### Segment 9

```
mov     eax, cs:op2
mov     ecx, cs:op1
sub     ecx, eax
mov     eax, ecx
mov     cs:result, eax
mov     edx, cs:result
lea     rcx, aResultIsD ; "result is %d "
call    j_printf
nop
jmp     short loc_140011977
```
This is the segment the code jumps to if user input was truly '-' as stated in [segment 5](#segment-5). After confirming that user input is really '-' it performs the subtraction operation and prints out the result. It corresponds to [Lines 19-21](#line-18-30) in the C code above.

### Segment 10

```
loc_140011977:
xor     eax, eax
lea     rsp, [rbp+0C8h]
pop     rdi
pop     rbp
retn
main endp
```

This is the last segment of relevant code, where the stack is being cleaned. It signifies the end of the main function. Corresponds to [Lines 29-30](#line-18-30) above.

The end.