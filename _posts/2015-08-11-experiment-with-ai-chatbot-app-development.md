---
layout: post
title: Experiment with AI chatbot app development
description: Developing artificial intelligence application is always an interesting project to do and AI chatbot is one of fun AI projects to get started.
keywords: artificial intelligence, chatbot, chatter bot, aiml, siml, elizabot.js
tags: [Artificial Intelligence, Chatbot]
comments: true
---

### Developing AI chatbot using mIRC script

Ten years ago I used a chatting program called [mIRC](http://www.mirc.com/) and it was quite popular at that time. This program was used to connect to [IRC](https://en.wikipedia.org/wiki/Internet_Relay_Chat) network and started chatting with other people. However, other than chatting, mIRC program had a feature called "remote" which allowed user to write some scripting inside. This feature gave me an ability for me to be creative and to have fun coding. So, I started to learn to write the script and kept doing it for years until one day I had an idea to write an artifical intelligence chatbot with the script.

Scripting with mIRC was really fun because a lot of scripts out there can do so many interesting things such as protecting channels from spamming or flooding, creating MP3 player where user can listen to MP3 while chatting without using former MP3 software, running a quiz bot in IRC channels, etc. Some people (scripters) wrote their script for "warring" - an activity to compete with other scripter to test how strong your script will be. Used to be "war scripter" for some time.

Come back to the topic, yes, I did build a chatbot script, a very simple chatbot program with direct input-output responses. There was no any markup language implemented. Not really "intelligence" after all. Almost the same concept with a quiz bot. At least, I already had this idea at the beginning.

### Developing AI chatbot using .NET Framework and SQLite database

After few years past, this "AI chatbot" idea thing came back into my mind. As this time, I developed them in .NET C# and using SQLite as the database. I used SQLite to save each possible input and output responses, so whenever the application received input that matched or similar, the application will immediately respond with possible outputs. Still no framework, and not really smart. Below are few screenshots about how the application is looked like:

[![WAYI v1](http://i.imgur.com/F1n1W0N.png)](http://i.imgur.com/F1n1W0N.png)

[![WAYI v1](http://i.imgur.com/IkAKC9p.png)](http://i.imgur.com/IkAKC9p.png)

[![WAYI v1](http://i.imgur.com/kMdAEpk.png)](http://i.imgur.com/kMdAEpk.png)

**Looking for framework..**

After I did some researches on Internet, I found one so called the "first framework" that I can start with. It was called [Artificial Intelligence Markup Language (AIML)](http://www.alicebot.org/aiml.html), a standard XML format markup language for defining the responses from the chatbot. AIML was developed by **Richard S. Wallace** and a worldwide free software community between 1995 and 2002. AIML formed the basis for what was initially a highly extended [Eliza](https://en.wikipedia.org/wiki/ELIZA) called ["A.L.I.C.E." (Artificial Linguistic Internet Computer Entity)](https://en.wikipedia.org/wiki/Artificial_Linguistic_Internet_Computer_Entity) which won the annual [Loebner Prize Competition](https://en.wikipedia.org/wiki/Loebner_Prize) in Artificial Intelligence three times and was also the Chatterbox Challenge Champion in 2004.

So, I used [AIMLbot.dll](http://aimlbot.sourceforge.net/) library for my chatbot application while getting myself to learn more about the markup language structures used in AIML from [this research paper](http://arxiv.org/ftp/arxiv/papers/1307/1307.3091.pdf). AIML provided much better way of defining the knowledge database of my chatbot and made it looked more natural to call as an "artificial intelligence" chatbot. Below are the screenshots of my chatbot that used AIML library.

[![WAYI v1](http://i.imgur.com/UJjTodD.png)](http://i.imgur.com/UJjTodD.png)
_WAYI v1_

[![WAYI v2](http://i.imgur.com/3mkEzII.png)](http://i.imgur.com/3mkEzII.png)
_WAYI v2_

### Developing AI chatbot using .NET Framework and SIML

After some time, I found another chatbot markup language known as [Synthetic Intelligence Markup Language (SIML)](http://simlbot.com/), which was more powerful than AIML. SIML provided much better features compared to AIML. So, I changed my chatbot current markup language from AIML to use SIML as SIML already provided their own chatbot studio program called Syn Chatbot Studio.

Based on their site, Syn Chatbot Studio offers a comprehensive collection of tools to develop intelligent chatbots that targeted desktops, mobile and web platforms. It has Code Analysis, AIML to SIML converter, JavaScript Editor, Regex Tester and smooth Auto-Complete. More interestingly, it has ability to execute JavaScript function from its routine of responses. That is very cool thing I think.. as a lot of things I can do here with JavaScript.

**Links:** [Wiki](http://wiki.syn.co.in/) / [GitHub](https://github.com/SynHub)

### Developing AI chatbot using JavaScript

"W4Y1", one of my latest AI chatbot experiments that uses JavaScript and available online. It's not a really chatbot where people can chat for any topic, because that is not my intention for now but it's more to so called a memory program to represent a part of myself as a fragment of knowledge in my mind.

If you have ever watched [I, Robot (2004)](http://www.imdb.com/title/tt0343818/) movie, yes, this is inspired from Dr. Alfred Lanning's hologram device used to leave his message for Spooner's investigation. While mine is just in the form of terminal layout, text format interaction and hosted online using GitHub Pages.

As for credits, I created W4Y1 based on [elizabot.js](http://www.masswerk.at/elizabot/) by **Norbert Landsteiner** as its interpretation engine for AI markup language and processing, [jQuery Terminal Emulator plugin](http://terminal.jcubic.pl/) by **Jakub Jankiewicz** for the program interface (GUI), and [particles.js](http://vincentgarreau.com/particles.js/) by **Vincent Garreau** for the particles effect in the background.

[![W4Y1](http://i.imgur.com/7emX4MU.png)](http://i.imgur.com/7emX4MU.png)
_The Screenshot_

**Live Demo:** [http://heiswayi.github.io/w4y1](http://heiswayi.github.io/w4y1)

Note: W4Y1 is just an experimental application, so expected not much features for the moment. However, there are few to-dos I might want to add into W4Y1 in the future development such as:-

* Integrate with [math.js](http://mathjs.org/) for mathematical computations.
* Implement AJAX request for accessing PHP files, so more features can be performed by PHP.
* Centralize the knowledge database somewhere and secure it.

As I grow older, my memory becomes weaker. Perhaps, an application like this in future may try to capture everything of my knowledge and able to recall or remember it for me one day once I forgot something.

### Conclusion

Artificial intelligence and the technology are one side of the life that always interest and surprise us with the new ideas, topics, innovations, products …etc. AI is still not implemented as the films representing it (i.e. intelligent robots), however there are many important tries to reach the level and to compete in market, like sometimes the robots that they show in TV. Nevertheless, the hidden projects and the development in industrial companies.

At the end, we’ve been in this research through the AI definitions, brief history, applications of AI in public, applications of AI in military, ethics of AI, and the three rules of robotics. This is not the end of AI, there is more to come from it, who knows what the AI can do for us in the future, maybe it will be a whole society of robots.
