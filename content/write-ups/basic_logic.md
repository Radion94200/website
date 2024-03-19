+++
title = 'basic_logic'
date = 2024-03-19
draft = false
categories = ['crackme']
tags = ['crackme','reverse']
+++

# Challenge description

Title            | Platform   | Language  | Difficulty
-----------------|------------|-----------|------------
basic_logic      | Unix/Linux | Assembler | 1.0

# Solution

The first part consists to get info about the file:

```bash
$ file logic
logic: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, stripped
```

We can open the file on `Cutter` to analyze its comportment.

```bash
entry0();
0x08048080      pop     eax        ; [00] -r-x section size 523 named .text
0x08048081      pop     eax
0x08048082      mov     dword data.EnterPassword, eax ; 0x80492d6
0x08048087      mov     eax, 4     ; syscall print
0x0804808c      mov     ebx, 1
0x08048091      mov     ecx, str.enter_password:___password_is_correct____password_is_incorrect__0123456789 ; segment.LOAD1
                                   ; 0x804928c
0x08048096      mov     edx, 0x10  ; 16 ; size of the str=16
0x0804809b      int     0x80       ; end syscall
0x0804809d      mov     eax, 0x14  ; 20 ; get pid syscall
0x080480a2      int     0x80
0x080480a4      mov     ebx, 0xa   ; ebx=0xa
0x080480a9      xor     ecx, ecx   ; init ecx to 0
0x080480ab      xor     edx, edx
; init edx
; = 0x62d47
0x080480ad      div     ebx
; div pid / ebx
; ebx = 0xa
0x080480af      push    dx
; push reminder
; rem = 7,0,8,4,0,4
0x080480b1      inc     cx
; cx = nb iterations
; cx =5
0x080480b3      test    eax, eax   ; Check if eax=0
0x080480b5      jne     0x80480ab  ; loop while eax != 0
0x080480b7      lea     edi, data.PidrestValues ; 0x80494fa ; load the data adress
0x080480bd      mov     word section..bss, cx ; 0x80492dc 
; set 4 bytes in bss
; bss=6
0x080480c4      pop     ax         ; set value of the heap in ax -> value of last dx push
0x080480c6      add     al, 0x30   ; 48 ; add 48 to al
0x080480c8      mov     byte [edi], al 
; set the al register and put it in edi
; edi =  6404807
0x080480ca      inc     edi        ; edi += 1 for each loop
0x080480cb      loop    0x80480c4  ; loop during ecx tour
0x080480cd      mov     eax, 0x1a  ; 26 ; syscall ptrace
0x080480d2      xor     ebx, ebx   ; set ebx = 0
0x080480d4      xor     ecx, ecx   ; set ecx=0
0x080480d6      mov     edx, 1
0x080480db      int     0x80       ; check if debug is present
0x080480dd      test    eax, eax   ; compare value and take exit jump if !=0
0x080480df      jns     0x80480e6  ; normal flow
0x080480e1      jmp     0x8048277  ; exit jump
0x080480e6      mov     eax, 0xd   ; 13 ; syscall syslseek
0x080480eb      mov     ebx, dword data.valuelseekrest ; 0x8049504 ; set offset to data
0x080480f1      int     0x80       ;  exec syscall
0x080480f3      mov     ebx, 0xa   ; ebx = 0xa
0x080480f8      xor     ecx, ecx   ; set ecx=0
0x080480fa      xor     edx, edx   ; set edx =0
0x080480fc      div     ebx        ; div eax/ebx
0x080480fe      push    dx
; push rest on heap
; edx = 1,1,4,7,5,8,0,1,7,1
0x08048100      inc     cx
; inc ec count
; cx = 0xa
0x08048102      test    eax, eax   ; check if eax=0
0x08048104      jne     0x80480fa  ; loop while eax!=0
0x08048106      lea     edi, data.valuelseekrest ; 0x8049504 ; load @data to edi
0x0804810c      mov     word data.lseekcount, cx ; 0x80492e6
; mov cx in data
; lssek = 0xa
0x08048119      inc     edi
0x0804811a      loop    0x8048113  ; same loop than before
0x0804811c      call    0x804823b
; call syscall ioctl
; eax=0                            ;  entry0(void)
0x08048121      mov     eax, 8     ; set eax=0x8
0x08048126      not     eax        ; eax=-9
0x08048128      and     dword [data.val_3], eax ; 0x80494e2
; set and value of eax in data
; eip = "\350\037\001"
0x0804812e      call    fcn.syscall_ioctl ; fcn.syscall_ioctl
;  call function
; eax=0
0x08048133      call    0x804823b  ; entry 0 -> sysctl ;  entry0(void)
0x08048138      mov     eax, 2     ; eax=2
0x0804813d      not     eax        ; eax=-3
0x0804813f      and     dword [data.val_3], eax ; 0x80494e2 ;  eax = eax and dw data
0x08048145      call    fcn.syscall_ioctl ; fcn.syscall_ioctl ;  call ioctl func
0x0804814a      xor     esi, esi   ; esi = 0
0x0804814c      cmp     esi, 0xe4  ; 228 ; compare esi = 228
0x08048152      je      0x804817c  ; esi= 228
0x08048154      mov     eax, 3     ; syscall read
0x08048159      mov     ebx, 1     ; file descriptor
0x0804815e      mov     ecx, data.userentry ; 0x80494d4 ; buffer
0x08048163      mov     edx, 1     ; size
0x08048168      int     0x80       ; syacall
0x0804816a      mov     al, byte data.userentry ; 0x80494d4 ; mov byte data in al
0x0804816f      cmp     al, 0xa    ; 10 ; check al=0x10
0x08048171      je      0x804817c  ; jump if eq 0x10
0x08048173      mov     byte [esi + data.080492f0], al ; 0x80492f0 ; set al in stack
0x08048179      inc     esi        ; esi+=1
0x0804817a      jmp     0x804814c  ; jump
0x0804817c      call    0x804823b  ; ioctl syscall ;  entry0(void)
0x08048181      or      dword [data.val_3], 8 ; 0x80494e2 ; or dw data of ioctl with 8
0x08048188      call    fcn.syscall_ioctl ; fcn.syscall_ioctl ; call ioctl func
0x0804818d      call    0x804823b  ; call entry ;  entry0(void)
0x08048192      or      dword [data.val_3], 2 ; 0x80494e2 ; or dw with 2
0x08048199      call    fcn.syscall_ioctl ; fcn.syscall_ioctl ; call ioctl function
0x0804819e      mov     esi, data.080492f0 ; 0x80492f0 ; mov ioctl value in esi
0x080481a3      call    fcn.check_bytes_esi ; fcn.check_bytes_esi ; check bytes esi
0x080481a8      cmp     ecx, 0
; eax=0?
; avoid this one -> password is incorrect
0x080481ab      je      0x804821e  ; if eax=0 j
0x080481ad      push    ecx        ; push ecx on stck
0x080481ae      mov     esi, data.PidrestValues ; 0x80494fa ; mov data in esi
0x080481b3      mov     edi, data.080493d4 ; 0x80493d4 ; mov data in edi
0x080481b8      mov     cx, word section..bss ; 0x80492dc ; mov bss in cx
0x080481bf      cld                ; clear direction flag
0x080481c0      rep     movsb byte es:[edi], byte ptr [esi] ; repeat movsb esi in edi
0x080481c2      mov     esi, data.valuelseekrest ; 0x8049504 ; mov data in esi
0x080481c7      xor     ecx, ecx   ; ecx=0
0x080481c9      mov     cx, word data.lseekcount ; 0x80492e6 ; ecx=data
0x080481d0      cld                ; clear direction flag
0x080481d1      rep     movsb byte es:[edi], byte ptr [esi]
; Concat chains in prebviously calculated
; 4048071710857411
0x080481d3      mov     esi, data.080493d4 ; 0x80493d4 ; mov data in esi
0x080481d8      call    fcn.check_bytes_esi ; fcn.check_bytes_esi ; set ecx with count
0x080481dd      pop     edx        ; edx= last stack value
0x080481de      cmp     ecx, edx
; cmp ecx,edx
; compare len of str and user input
0x080481e0      jne     0x804821e  ; jmp to end
0x080481e2      mov     ecx, edx   ; ecx=10
0x080481e4      xor     edx, edx   ; set edx=0
0x080481e6      mov     esi, data.080492f0 ; 0x80492f0
; mov data in esi
; user entry
0x080481eb      mov     edi, data.080493d4 ; 0x80493d4
; mov data in edi
; 4048071710857411
0x080481f0      cmp     ecx, edx   ; ecx=adx?
0x080481f2      je      0x8048201  ; check condition
0x080481f4      mov     al, byte [esi] ; mov byte esi in al
0x080481f6      mov     bl, byte [edi] ; mov byte edi in bl
0x080481f8      cmp     al, bl     ; comp al,bl
0x080481fa      jne     0x804821e  ; jump to exit
0x080481fc      inc     esi        ; esi+=1
0x080481fd      inc     edi        ; edi+=1
0x080481fe      inc     edx        ; edx+=1
0x080481ff      jmp     0x80481f0  ; jump °0x80481f0
0x08048201      mov     eax, 4     ; syscall write
0x08048206      mov     ebx, 1     ; file descriptor
0x0804820b      mov     ecx, data.CorrectPassword ; 0x804929c ; buffer = password is correct
0x08048210      mov     edx, 0x17  ; 23 ; size buffer
0x08048215      int     0x80       ; syscall
0x08048217      mov     eax, 1     ; syscall exit
0x0804821c      int     0x80       ; sycall
0x0804821e      mov     eax, 4     ; syscall write
0x08048206      mov     ebx, 1     ; file descriptor
0x0804820b      mov     ecx, data.CorrectPassword ; 0x804929c ; buffer = password is correct
0x08048210      mov     edx, 0x17  ; 23 ; size buffer
0x08048215      int     0x80       ; syscall
0x08048217      mov     eax, 1     ; syscall exit
0x0804821c      int     0x80       ; sycall
0x0804821e      mov     eax, 4     ; syscall write
0x08048223      mov     ebx, 1     ; file descriptor
0x08048228      mov     ecx, data.IncorrectPassword ; 0x80492b3 ; buffer = password is incorrect
0x0804822d      mov     edx, 0x19  ; 25 ; size
0x08048232      int     0x80       ; syscall
0x08048234      mov     eax, 1     ; syscall exit
0x08048239      int     0x80       ; syscall
0x0804823b      mov     eax, 0x36  ; '6' ; 54 ; sycall ioctl
0x08048240      mov     ebx, 1     ; param 1
0x08048245      mov     ecx, 0x5401 ; param 2
0x0804824a      mov     edx, data.080494d6 ; 0x80494d6 ; param 3
0x0804824f      int     0x80       ; syscall
0x08048251      ret
fcn.syscall_ioctl();
0x08048252      mov     eax, 0x36  ; '6' ; 54 ; syscall ioctl
0x08048257      mov     ebx, 1     ; arg1 = file descriptor
0x0804825c      mov     ecx, 0x5402 ; arg2 = devide dependant request-code
0x08048261      mov     edx, data.080494d6 ; 0x80494d6 ; arg3 = untyped pointer to memory
0x08048266      int     0x80       ; syscall
0x08048268      ret
fcn.check_bytes_esi();
0x08048269      mov     ecx, 0     ; set ecx=0
0x0804826e      lodsb   al, byte [esi] ; load bytes @esi in al
0x0804826f      or      al, al     ; test al
0x08048271      je      0x8048276  ; if eq jump ret
0x08048273      inc     ecx        ; inc ecx
0x08048274      jmp     0x804826e  ; loop function
0x08048276      ret
0x08048277      mov     eax, 0xa   ; syscall sysunlink
0x0804827c      mov     ebx, dword data.EnterPassword ; 0x80492d6
0x08048282      int     0x80       ; call sysunlink
0x08048284      mov     eax, 1     ; sysexit
0x08048289      int     0x80       ; call sysexit
```

*Some lines may be missing in the code above due to a bad copy/paste and the length of the code*

To conclude the analyze of this crackme we can understand that it get the PID of the program and the timestamp of the execution.
Then it concat them and check if the user entry and data lauched at start are equals.

If yes you got the win message.
If no you can try again.

In my case I add some comments to explain how to find them.
For example with password = `4048071710857411`, it's a win.
I get this value with `gdb`.

That's All Falks!
