---
title: GhostBot [OSINT]
published: 2024-12-27
description: 'Hard OSINT challenge from 0xl4ugh CTF'
image: './img/lol0xlfugh.png'
tags: ["Hard", "First Blood"]
category: 'OSINT'
draft: false 
lang: ''
---

## Challenge Description

> I discovered a leaked Discord chat where two individuals discussed a powerful bot used to track cybercriminals. One plans to use the bot to target someone, while the other warns of the risks. I’ve decided to investigate the bot’s origin, its role in cybercrime, and who's controlling it. Flag Format: 0xL4ugh{}

![discordleak](./img/discordleak.png "discordleak")

## Solution

Theres a few key points to consider when doing our research for this challenge
1. We get 2 names **Za8lola** and **7amoksha** from the chat
2. We also see someone else named **MM0X** who was talking about the bot in a discord server
3. Its been tracking criminals since 2021

Doing mass searches for these users returns nothing useful.
From our discord server I couldnt find any users named **Za8lola** or **7amoksha** but I did see someone named **MM0X** who shares the other discord server with me 

> [!NOTE]
> Both these servers are for the same CTF
![mmox](./img/mmox.png "mmox")

Since we have a clue that its been working since 2021 I can search for all his posts starting from the oldest

![2021](./img/2021.png "2021")

I noticed that theres an unknown channel/server that hes referring to here however its unavailable

![noaccess](./img/noaccess.png "noaccess")

If you right click on that unknown server you can copy the link we’ll be able to get the server ID. https://discord.com/channels/1321167893559382100/1321167894163226737/1321189372854276098

From this I useed an online tool (https://discord-avatar.com/en/server) to search Discord server by ID and found this server 

![serverid](./img/serverid.png "serverid")

I joined the discord server and saw a few familiar names. You'll see **MM0X** and **7amoksha** from the screenshot

I also saw the bot that we’re after called **Made7a**

This bot has a few commands

![commands](./img/commands.png "commands")

```
!list will print out a list of users in the database
!info will give us the info about that user
!login will generate a jtw for us to use. This token is role: guest by default
!login_with_token will just take a token parameter and login with it
```

I first got the list of users 

![userlist](./img/userlist.png "userlist")

I tried every user with !info and got a result for each except for **Elsfa7 Elmrta7** which needs admin user to use it. This is probably where the flag will be (it was).

![notauth](./img/notauth.png "notauth")

I tried to do some JWT bypasses and brute force to try and get the key but it dosent work so then I dug deeper.

> [!IMPORTANT]
> If you click on **7amoksha**’s profile you'll see that his careless actions have caused him to become a victim of the hacker.
![victim](./img/victim.png "victim")

The URL Topgg is a Discord bot website but the actual link is invalid. However, with the new name **Apachei** I used it to get some more info.
I did a Sherlock search and got a ton of links. The only useful one is the Github.

![sherlock](./img/sherlock.png "sherlock")

This guy has one repo (base64 decodes to the bots name)

![github](./img/github.png "github")

The current <code>Botdis.py</code> file contains a rick roll gif

![rr](./img/rr.png "rr")

Theres 6 commits I looked through 

![commits](./img/commits.png "commits")

The <code>.patch</code> file returns a name and an email that was not part of the challenge
The second commit shows the entire backend code including a secret key

![secret1](./img/secret1.png "secret1")

I tried using the key to craft the JWT but it dosent work so he must have changed it
Commit 4 also contains this comment. The ID just relates to **7amoksha**’s discord profile

![id](./img/id.png "id")

Commit 5 removes the source code and he BMs us in the comments some more

![bm](./img/bm.png "bm")

> [!NOTE]
> From here we kinda messed up and first blooded the challenge the wrong way. My teammate rcopstein noticed that the bot would capitalize the first character of each word you gave it
He came up with an exploit for it which would give the key
![exploit](./img/exploit.png "exploit")

The correct way however is to find the hackers gist account which contains a single gist that has the secret key

![gist](./img/gist.png "gist")

I used the key to craft the admin JWT and get the info for **Elsfa7 Elmrta7**

![jwt](./img/jwt.png "jwt")

### FLAG: ``0xL4ugh{Y0u_So1v3d_m3_bu7_It5_N0t_th3_End}``