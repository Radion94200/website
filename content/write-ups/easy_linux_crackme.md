+++
title = 'easy_linux_crackme'
date = 2024-03-16
draft = false
categories = ['crackme']
tags = ['crackme','reverse']
+++

# Challenge description

Title               | Platform   | Language  | Difficulty
--------------------|------------|-----------|------------
easy_linux_crackme  | Unix/Linux | Assembler | 1.0

# Solution

The first step is to get some informations about the file:

```bash
$ file blah
blah: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, stripped
```

It's a basic 32 bit linux executable.
We can use `Cutter` to decompile the file.

```bash
  ;-- section..text:
entry0();
0x08048094      xor     eax, eax   ; init EAX to 0
0x08048096      mov     eax, 0x2f  ; '/' ; 47 -> set the value in eax
0x0804809b      int     0x80 -> syscall interruption
0x0804809d      cmp     eax, 0xdead; compare the value of eax and 0xdead
0x080480a2      jne     0x80480ba  ; if not equal, jmp to 0x80480ba
0x080480a4      mov     eax, 4     ; starts new syscall
0x080480a9      mov     ebx, 1
0x080480ae      mov     ecx, str.Okej ; write okej on the screen
0x080480b3      mov     edx, 6
0x080480b8      int     0x80       ; end syscall
0x080480ba      xor     eax, eax
0x080480bc      inc     eax
0x080480bd      xor     ebx, ebx
0x080480bf      int     0x80
```

We can see there is not a lot of code in this part.

The reverse is done.

The easier way to get the `Okej` is to create a group with an `id=0xdead`.
The relaunch the program and it's a win.

The other way to print the sentence `Okej` we need to patch the program.
There are lots of possibilities to patch it, we can set the value of the register eax to 0xdead, or we can simply doing this step by changing the jne to nop for example.
The other way is to execute dynamically (via gdb) the program and to set the value of eax to 0xdead then we can see the win sentence.

That's All Falks
