+++
title = 'crackme-not'
date = 2024-03-16
draft = false
categories = ['crackme']
tags = ['crackme','reverse']
+++

# Challenge description

Title            | Platform   | Language  | Difficulty
-----------------|------------|-----------|------------
crackme-not      | Unix/Linux | Assembler | 1.0

This is the first crackme i ever created, also the first piece of asm code i ever wrote, as you can take from the name, it grew from a hello-world example.
I still would rate it as difficulty 2 since most level 1 crackmes are kind of obvious to solve once you open them in some debugger.

# Solution

The first part consists to get info about the file:

```bash
$ file hello
hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, not stripped
```

We can open the file on `Cutter` to analyze its comportment.

```bash
entry0();

# Check if the name is not empty
    0x00401000      mov     eax, 1     ; [01] -r-x section size 301 named .text
    0x00401005      mov     edi, 1
    0x0040100a      movabs  rsi, msg   ; 0x402000 ; Please enter your name:
    0x00401014      mov     edx, 0x19  ; 25
    0x00401019      syscall
    0x0040101b      mov     eax, 0     ; syscall write
    0x00401020      mov     edi, 0
    0x00401025      movabs  rsi, buf   ; 0x402074 ; get name and put it in buf of size=32
    0x0040102f      mov     edx, 0x20  ; 32
    0x00401034      syscall
    0x00401036      cmp     rax, 0
    0x0040103a      jl      _start.error

# Set buffers
    0x00401040      mov     r14, rax   ; set size of buf
    0x00401043      add     r14, 6
    0x00401047      mov     rax, qword hello ; 0x402019 ; set hello in RAX
    0x0040104f      mov     qword welcome, rax ; 0x402094 ;  set hello in welcome array
    0x00401057      mov     rax, qword buf ; 0x402074 ;  set qword of rsi/buf in rax
    0x0040105f      mov     qword data.0040209a, rax ; 0x40209a ;  set rax in data array
    0x00401067      mov     eax, 1
    0x0040106c      mov     edi, 1
    0x00401071      movabs  rsi, welcome ; 0x402094 ;  write hello + entry user
    0x0040107b      mov     rdx, r14   ; 0x24 -> size entry
    0x0040107e      syscall
    0x00401080      mov     eax, 1     ; syscall write
    0x00401085      mov     edi, 1
    0x0040108a      movabs  rsi, prompt ; 0x402020 ;  write password on screen
    0x00401094      mov     edx, 0x16  ; 22
    0x00401099      syscall
    0x0040109b      mov     eax, 0
    0x004010a0      mov     edi, 0
    0x004010a5      movabs  rsi, buf   ; 0x402074 ; get password from user entry
    0x004010af      mov     edx, 0x20  ; 32
    0x004010b4      syscall
    0x004010b6      mov     r15, rax   ;  set size of user entry in r15
    0x004010b9      dec     r15        ;  delete \n,

# Check the password
;-- _start.l1:
    0x004010bc      mov     r14, r15   ; set size of the entry to r14
    0x004010bf      add     r14, 5     ;  add 5 to user entry size
    0x004010c3      mov     al, byte [r14 + welcome] ; 0x402094 ; set each letter of password in al
    0x004010ca      add     al, 5      ; add 5 to the value of the letter
    0x004010cc      cmp     al, byte [r15 + 0x402073] ; password from user entry
---- if compare above fail the prog exit ---
    0x004010d3      jne     _start.wrong
--- test if r15!=0 ---
    0x004010d5      dec     r15
--- if not equal 0 relauch the previous part
    0x004010d8      jne     _start.l1
--- end the prog ---
    0x004010da      mov     eax, 1
    0x004010df      mov     edi, 1
    0x004010e4      movabs  rsi, success ; 0x402053
    0x004010ee      mov     edx, 0x18  ; 24
    0x004010f3      syscall
    0x004010f5      jmp     _start.exit

# Bad password
;-- _start.wrong:
    0x004010f7      mov     eax, 1
    0x004010fc      mov     edi, 1
    0x00401101      movabs  rsi, wrong ; 0x402036
    0x0040110b      mov     edx, 0x18  ; 24
    0x00401110      syscall
    0x00401112      jmp     _start.exit

# If the name is empty
;-- _start.error:
    0x00401114      mov     qword ret_code, rax ; 0x402070

# End of the program
;-- _start.exit:
    0x0040111c      mov     eax, 0x3c  ; '<' ; 60
    0x00401121      movabs  rdi, ret_code ; 0x402070
```

Just above, we have the decompiled code.
We can start the analyse.

The analyse of the code is in comment (after ;) in the code above.

To conclude, the important part is the function `start_l1`. This function check if the password correspond to the username with a shift of 5.
IE: a -> f

We can check our result in dynamic:

```bash
$ ./hello
Please enter your name: hello
Hello hello
Enter your Password: mjqqt
Great H4x0r Skillz!%
```

That's All Falks
