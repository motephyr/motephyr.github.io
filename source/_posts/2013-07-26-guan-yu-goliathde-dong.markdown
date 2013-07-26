---
layout: post
title: "關於Goliath的洞"
date: 2013-07-26 22:29
comments: true
categories: 
---
最近持續的研究Goliath

原本想要做的事情：
Java (Spring Framework)--ActiveMQ(JMS)--WebSocket(Goliath)

白話文： 
將從java過來的訊息透過java message service以goliath server接收，最後以WebSocket協定送至畫面上。

###選擇MQ時考慮過的其他做法
######AMQP(Advanced Message Queuing Protocol)
要不是不想動MQServer(需要換成RabbitMQ)，這個做法其實比較標準
######STOMP
很簡單的架構，但不太夠用。可是jms就非得要用jruby做，就去找了jruby-jms這個套件來處理

結果！結果！Goliath硬是和jruby不合，跑起來就是不對勁

一下子頁面出不來

一下子Message接不到

一下子WebSocket莫名其妙斷掉

看來還是不要逆天行事，以後大家還是乖乖用AMQP做事好了，研究完WebSocket的部分，再來好好介紹AMQP。

