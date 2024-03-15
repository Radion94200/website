+++
title = 'EZ crackme'
date = 2024-03-15
draft = false
categories = ['crackme']
tags = ['crackme','reverse']
+++

# Challenge description

Title            | Platform   | Language  | Difficulty
-----------------|------------|-----------|------------
EZ crackme       | Unix/Linux | Assembler | 1.0

its your time to learn using this basic example

# Solution

The first step is to obtain informations about the file:

```bash
$ file run.exe
run.exe: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, with debug_info, not stripped
```

The second step is to open the file in a disass. Currently I am using `Cutter`.
Once done, there are not a lot of code:

```bash
entry0();
0x08049000      pop     ebx        ; code.asm:5 ; [00] -r-x section size 69 named .text
0x08049001      pop     ebx        ; code.asm:6
0x08049002      pop     ebx        ; code.asm:7
0x08049003      mov     eax, dword password ; code.asm:8 ; 0x804a000
0x08049008      cmp     eax, dword [ebx] ; code.asm:9
0x0804900a      je      _start.goodjob ; code.asm:10
0x0804900c      jmp     _start.wrong ; code.asm:11
;-- _start.goodjob:
0x0804900e      mov     eax, 4     ; code.asm:13
0x08049013      mov     ebx, 1     ; code.asm:14
0x08049018      mov     ecx, goodjobText ; code.asm:15 ; 0x804a008
0x0804901d      mov     edx, 0xe   ; code.asm:16 ; loc.goodjobLen
0x08049022      int     0x80       ; code.asm:17
0x08049024      jmp     _start.end ; code.asm:18
;-- _start.wrong:
0x08049026      mov     eax, 4     ; code.asm:20
0x0804902b      mov     ebx, 1     ; code.asm:21
0x08049030      mov     ecx, nope  ; code.asm:22 ; 0x804a016
0x08049035      mov     edx, 7     ; code.asm:23
0x0804903a      int     0x80       ; code.asm:24
;-- _start.end:
0x0804903c      mov     eax, 1     ; code.asm:26
0x08049041      xor     ebx, ebx   ; code.asm:27
0x08049043      int     0x80       ; code.asm:28
```

There is an other string in the code:

```bash
0x0804a000      .string "P455w0rdYou Got This!\nWrong!\n" ; len=29
```

We can see there are multiple sections in this code.
Only 3 parts interest us:

- entry0
- start.goodjob
- string

We start reading the assembly:

In instruction `0x08049003` we can see that a dword from password (the previous string) is put in the eax register.
This is the part `P455` of the string.

Then we compare this value to the user entry in the `0x08049008` instruction -> the comparison starts at th 8th char of the string and finish at the 14th with `0xe`.
If the comparison id ok we jump to `0x0804900a` and we print the goodjob text.
As the dword of the string already move to eax, the string was: You Got This!\nWrong!\n.
So the first 14th char are: You Got This!\n

**Flag**: P455

So to test our theory, we can test:

```
$ ./run.exe P455
You Got This!
```

That's All Falks
