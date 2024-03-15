+++
title = 'easy_crackme_2 by lord'
date = 2024-03-16
draft = false
categories = ['crackme']
tags = ['crackme','reverse']
+++

# Challenge description

Title                  | Platform   | Language  | Difficulty
-----------------------|------------|-----------|------------
easy_crackme_2 by lord | Unix/Linux | Assembler | 1.0

# Solution

The first part consists to get informations about the file:

```bash
$ file cm1eng
cm1eng: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, stripped
```

It's a basic 32 bit linux executable.
We can use `Cutter` to obtain the disassembly of the code:

```bash
;-- section..text:
entry0();
# syscall
0x08048080      mov     eax, 4     ; [00] -r-x section size 118 named .text
0x08048085      mov     ebx, 1
0x0804808a      mov     ecx, str.Password_: ; segment.LOAD1
                                   ; 0x80490f8
0x0804808f      mov     edx, 0xd   ; 13 -> size of the str print on the screen
0x08048094      int     0x80
# End of the syscall -> print "\npassword: "

0x08048096      mov     edx, 0x100 ; 256
0x0804809b      mov     ecx, data.0804911b ; 0x804911b set wining sentence in ecx
0x080480a0      mov     ebx, 0
0x080480a5      mov     eax, 3
0x080480aa      int     0x80
0x080480ac      mov     esi, str.QTBXCTU ; 0x8049126 -> set string QTBXCTU in esi
0x080480b1      mov     edi, esi         ; set value of esi in edi
0x080480b3      xor     ebx, ebx         ; set ebx=0
0x080480b5      cld                      ; clear direction flag

# Loop to create the password ans check the size
0x080480b6      lodsb   al, byte [esi]   ; put each byte of the password in
0x080480b7      xor     al, 0x21   ; 33 -> xor the letter of QTBXCU with 0x21
0x080480b9      stosb   byte es:[edi], al
0x080480ba      inc     ebx
0x080480bb      cmp     ebx, 7     ; 7 -> check the size of user entry = 7
0x080480c1      je      0x80480c5
0x080480c3      loop    0x80480b6
# End of the loop

# Set data and user entry in edi and esi
0x080480c5      mov     esi, data.0804911b ; 0x804911b
0x080480ca      mov     edi, str.QTBXCTU ; 0x8049126
0x080480cf      mov     ecx, 7
0x080480d4      cld                       ; clear direction flag

# Compare str in each register byte by byte
0x080480d5      repe    cmpsb byte [esi], byte ptr es:[edi]
0x080480d7      jne     0x80480ef ; if not equal then jump

# win syscall
0x080480d9      mov     eax, 4
0x080480de      mov     ebx, 1
0x080480e3      mov     ecx, str.Great_you_did_it__: ; 0x8049105
0x080480e8      mov     edx, 0x16  ; 22
0x080480ed      int     0x80

# End program
0x080480ef      mov     eax, 1
0x080480f4      int     0x80
```

The code is not so long, so we can analyse it.

As we can see we have the flag with the comparison.
We can use the basic string `QTBXCTU` and xor each byte with `0x21`. We can use python to find the password:

```python
>>> chr(ord('Q')^0x21) + chr(ord('T')^0x21) + chr(ord('B')^0x21) + chr(ord('X')^0x21) + chr(ord('C')^0x21) + chr(ord('T')^0x21) + chr(ord('U')^0x21)
'pucybut'
```

So, the flag is `pucybut`.

We can check our result by executing the program:

```
$ ./cm1eng

Password : pucybut
Great you did it !:)

```

That's All Falks
