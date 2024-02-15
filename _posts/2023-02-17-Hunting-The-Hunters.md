---
title: Hunting the Hunters
date: 2023-02-17 11:33:00 +0800
categories: [0days]
tags: [0days]
math: true
mermaid: true
comments: true
---
<script src="https://fastly.jsdelivr.net/npm/lazysizes@5.3.2/lazysizes.min.js" defer></script>

## Intro

I was trying to automate my bug hunting process so i searched for public tools that automate reconnaissance i found [Rengine](https://github.com/yogeshojha/rengine) which is a web application reconnaissance suite with focus on a highly configurable streamlined recon process via Engines, recon data correlation, continuous monitoring, recon data backed by a database, and a simple yet intuitive User Interface.

## Setup

After installing rengine on my VPS, I attempted to hide the dashboard by configuring the port to only be accessible through localhost. During this process, I discovered that multiple ports were exposed through Docker, including the database port. As a result, I began to investigate further and found that there were hard-coded credentials for both the database and the dashboard during installation. `Note that newer versions of rengine include warning messages to prompt users to change these credentials.`

## Reconnaissance

i used shodan to search for rengine instances using simple dork `http.html:"Login to reNgine"`

![RengineScan](/images/rengine_scan.png)

![shodanscan](/images/shodan_scan.png)

## Bruteforce

I used an Nmap script to brute-force the username and password for the PostgreSQL database using the hard-coded credentials that I found during the installation process. As a result, I discovered multiple instances that were misconfigured and using the default password.

![namp](/images/nmap_shodan.png)

i made a small filtering to extract only the ips that are misconfiguraed

![pwned](/images/pwned_parsed.png)

## Account takeover

I am aware that the Rengine web application is built using the Django framework, and I have access to its database. After conducting some research, I discovered that Django applications store session data in a table named 'dj_sessions'.

![database](/images/Database.png)

the table has 3 columns username,session_key,expire_date

![djsession](/images/djsesion.png)

so i toke the ip and the session key and edited the cookie

![session](/images/session_structure.png)

I was able to take over the root account of the Rengine instance. Additionally, I discovered hundreds of misconfigured Rengine instances

![editcookie](/images/Editcookie.png)

![pwned](/images/pwned1.png)
