+++
title = 'From Dusk Till Down'
date = 2020-11-03
draft = false
categories = ['ctf']
tags = ['ecw','misc']
+++

# Challenge description
This challenge was part of the European Cyber Week Qualification.

![FDTD](/ecw/ecw-fdtd.png "From Dusk Till Down Challenge"){:.centered}

This goal of this challenge is to find the next number which must be print.

# Solve

After some researches on Google to understand the title of the challenge, it appears that's the name of a film. On the wikipedia page in the `synopsis` part there is a link with Twister. In programmation, twister refers to the mersenne twister generator.

The advantage of this number generator is that it's a pseudo random generator. So we can predict the next number.

**ATTENTION**: keep the ssh session open during the operation else the seed will be different and we need to start again.

We connect in ssh to the challenge. Then we launch the challenge. The challenge asks us a number to generate a set of values. We take 10 values and put them in a file `input.txt`. Once these values are generated, we need to find a mersenne generator to brute force the seed.

We can use this repo: [Untwistter](https://github.com/hyprwired/untwister)

Then with the following command we can brute force the seed:

```bash
./untwister -i input.txt -d 1
```
*It takes a long time (~10 min).*

Once the seed is find, we can generate the list of numbers create with this seed. The command is:</p>

```bash
./untwister -g 6706650
```
Then the list appear. As we asked 10 numbers, we take the next one (29732694) and send it on the ssh.
Catch the flag and it's done.
