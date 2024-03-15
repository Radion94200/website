+++
title = 'FindThePassword1'
date = 2024-03-15
draft = false
categories = ['crackme']
tags = ['crackme','reverse']
+++

# Challenge description

| Title | Platform | Language | Difficulty |
| :---: | :---: | :---: | :---: |
| FindThePassword1 | Unix/Linux | Assembler | 1.0 |

If you aren't a _beginner_, you will get bored very quickly. If you are a beginner, this will help you grasp basic concepts.

# Solution

The first step is to `un7z` the file:

```bash
$ 7z x findthepassword1.tar.7z

7-Zip 23.01 (x64) : Copyright (c) 1999-2023 Igor Pavlov : 2023-06-20
 64-bit locale=C.UTF-8 Threads:8 OPEN_MAX:1024

Scanning the drive for archives:
1 file, 2344 bytes (3 KiB)

Extracting archive: findthepassword1.tar.7z
--
Path = findthepassword1.tar.7z
Type = 7z
Physical Size = 2344
Headers Size = 146
Method = LZMA2:24k
Solid = -
Blocks = 1

Everything is Ok

Size:       20480
Compressed: 2344
```

Once done the second step is to `untar` the file:

```bash
$ tar -xvf findthepassword1.tar
findthepassword1/readme.asm
findthepassword1/findthepassword1
findthepassword1/
```

We get a new folder with 2 files:

- readme.asm -> a simple readme file
- findthepassword1 -> the crackme challenge

We can start the analyze.
The first thing is doing a `strings` on the file.

```bash
$ strings findthepassword1
+ ------------------- +
| Find the Password 1 |
|           by Xeeven |
+ ------------------- +
Password: + *** *** *** *** *** +
|   Congratulations!  |
+ *** *** *** *** *** +
Wrong! Try again.
8675309
.shstrtab
.text
.data
.bss
```

The only one string which appear is `8675309`.

We can test to execute the program with this code:

```bash
$ ./findthepassword1
+ ------------------- +
| Find the Password 1 |
|           by Xeeven |
+ ------------------- +
Password: 8675309
+ *** *** *** *** *** +
|   Congratulations!  |
+ *** *** *** *** *** +
```

That's All Falks
