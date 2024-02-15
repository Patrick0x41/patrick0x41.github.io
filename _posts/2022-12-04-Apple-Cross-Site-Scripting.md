---
title: Apple Cross-Site Scripting
date: 2022-12-04 11:33:00 +0800
categories: [Bug Hutning]
tags: [Bug Hutning]
math: true
mermaid: true
comments: true
---
<script src="https://fastly.jsdelivr.net/npm/lazysizes@5.3.2/lazysizes.min.js" defer></script>

Last year i was able to find cross-site scripting in one of apple's subdomains moreover i was able to bypass the firewall

## Scanning

after i made subdomain enumeration and parameter fuzzing i found there is a parameter is refleceted in one of the subdomains

![Reflected-Parameter](/images/reflection.png)

![Reflected-Parameter](/images/chrome_6EGCRMWxBi.png)
i tried to to use an event handler to execute a javascript as a proof of concept unfortunately the web application firewall blocked it
![Blocked](/images/chrome_s5ryEJ6hXi.png)

## Fuzzing the web application firewall

the firewall was blocking the event handlers so i thought maybe some event handlers are not blocked i got wordlist of javascript event handlers from <a href="https://portswigger.net/web-security/cross-site-scripting/cheat-sheet">PortSwigger</a>
![Wordlist](/images/wordlist.png)
i used the burp intruder to make brute force using a list of event handlers to check if there is a event handler that is not blocked and i found multiple event handlers that are not blocked
![BurpIntruder](/images/intruder.png)
The final payload

```javascript
" OnPointerEnter=top[/al/.source+/ert/.source](1)
```

![Final-Payload](/images/chrome_TenRXrR9Xy.png)
i was able to bypass the web application firewall
![xss](/images/xss.png)
![https://media.giphy.com/media/l4EpkVLqUj8BI7OV2/giphy.gif](https://media.giphy.com/media/l4EpkVLqUj8BI7OV2/giphy.gif)

## Google Dorks is here to help

wait i haven't finished yet i said what if this endpoint is duplicated in another domain i made a simple google dork using keywords i found in the page and i was able to find another domain having the same vulnerable endpoint
![second](/images/second.png)

## Apple hall of fame

i reported the vulnerability to apple and they put my name in the hall of fame
![apple_response.png](/images/apple_response.png)
![HallofFame](/images/HOF.png)
