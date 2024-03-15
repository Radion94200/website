+++
title = 'nasm crack'
date = 2024-03-15
draft = false
categories = ['crackme']
tags = ['crackme','reverse']
+++

# Challenge description

Title            | Platform   | Language  | Difficulty
-----------------|------------|-----------|------------
nasm crack       | Unix/Linux | Assembler | 1.0

Welcome to this easy crackme. Program made with nasm for 64bit intel. Have fun! Can you receive the "Correct!" message?

# Solution

The first step is get informations about the file:

```bash
$ file nasm_crack
nasm_crack: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, not stripped
```

It's a basic 64 bits linux file.

Then we can open the file in `Cutter`:

```bash
;-- correct_func:
0x00401000      mov     eax, 1     ; [00] -r-x section size 162 named .text
0x00401005      mov     edi, 1
0x0040100a      movabs  rsi, correct ; 0x402016
0x00401014      mov     edx, 9
0x00401019      syscall
0x0040101b      mov     eax, 0x3c  ; '<' ; 60
0x00401020      mov     edi, 0
0x00401025      syscall
0x00401027      ret
  ;-- _start:
entry0();
0x00401028      mov     eax, 1
0x0040102d      mov     edi, 1
0x00401032      movabs  rsi, msg1  ; 0x402000
0x0040103c      mov     edx, 0x16  ; loc.len1
0x00401041      syscall
0x00401043      mov     eax, 0
0x00401048      mov     edi, 0
0x0040104d      movabs  rsi, input ; loc._edata
                                   ; 0x402031
0x00401057      mov     edx, 0x10  ; 16
0x0040105c      syscall
0x0040105e      movabs  rdi, passwd ; 0x402026
0x00401068      movabs  rsi, input ; loc._edata
                                   ; 0x402031
0x00401072      mov     ecx, 0xb   ; 11
0x00401077      repe    cmpsb byte [rsi], byte ptr [rdi]
0x00401079      je      correct_func
0x0040107b      mov     eax, 1
0x00401080      mov     edi, 1
0x00401085      movabs  rsi, wrong ; 0x40201f
0x0040108f      mov     edx, 7
0x00401094      syscall
0x00401096      mov     eax, 0x3c  ; '<' ; 60
0x0040109b      mov     edi, 0

;-- msg1:
0x00402000          .string "Enter your password: "

```

We can now analyze the code.

Firstly, we can see there is a call to `msg1` -> the sentence "Enter your password: " will appear on the screen.

Then we can see an input is asked then is stored in `0x402031`.
After this step we can see there is `0x10` set in edx -> the offset for the next string.
Now, the `passwd` value is set in `rdi` -> the string is `supersecret` and then there is a comparison between our entry and this value.

If the both values are equals, then we move to correct function, else to the wrong function.
The correct function print `Correct!` on the screen then done.

We can now check if dynamic:

```
./nasm_crack
Enter your password: supersecret
Correct!
```

That's All Falks
