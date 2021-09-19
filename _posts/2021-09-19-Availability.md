---
description: >-
  kozmer’s writeup on the Availability challenge from H@cktivityCon 2021
layout: post
title: H@cktivityCon 2021 - Availability Writeup
date: 2021-09-19 20:00:00
categories: H@cktivityCon 
tags: [ctf]
pin: true
---

## Overview
![](../../assets/img/h@cktivitycon_2021/Availability/index.png)

#### Digging in

Looking more into the webapp, it is similar to previous challenges from this series such as Integrity, the main difference being that this is a Blind Command Injection Vulnerability. This means we cannot view the output of payloads we send such as `cat`, therefore we will have to send the output to a server. If you dont have a server to work with, ngrok is a good solution to solve this problem.

----

![](../../assets/img/h@cktivitycon_2021/Availability/burp_req.png)

As you can see from the burp request from above, we `cat` the contents of the flag and send it to our server. The bypass with `%0a` is the same from the challenge I mentioned before: `Integrity`.


![](../../assets/img/h@cktivitycon_2021/Availability/flag.png)

As you can see from above, the payload worked and we received the flag!
