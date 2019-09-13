---
template: post
title: How can we use Redis Queue mechanism in Node.js?
slug: /how-can-we-use-redis-queue-mechanism-in-nodejs
draft: false
date: 2019-09-13T06:05:24.482Z
description: How can we use Redis Queue in a best way in Node.js?
category: 'Node.js, Redis'
tags:
  - Node.js
  - Redis
  - Redis Queue
---
## Overview

In this article, we'll learn about redis queue mechanism. 

We have to process the data in sequential manner one after another, then we can think about queue mechanism. 

## Get Started

First we'll init package file and then will install redis there-

```
mkdir redis-app
cd redis-app
npm init
npm install --save redis
```

create new file in redis-app folder, we'll try to connect with redis-server there (index.js) - 

```
const redis = require("redis");
let redisNotReady = true;
let redisClient = redis.createClient({
    host: '127.0.0.1',
    port: 6379
});

redisClient.on("error", (err) => {
   console.log("error", err)
});

redisClient.on("connect", (err) => {
    console.log("connect");
});

redisClient.on("ready", (err) => {
    redisNotReady = false;
    console.log("ready");
});
```

**Redis Queue Mechanism -**

We have to process the data in sequential manner one after another in one key and one key's value will have complete list. We'll push the data from right side and pop the data from left side and get the left most data from list. Here we'll use lrange, lpop, rpush method of redis to perofrm this queue mechanism. Please take a look to below image for more clarification - 

![](/media/untitled-diagram-7-.jpg)

**rpush method -** 
We can push one or multiple item in one chunk.

```
redisClient.rpush(['test-key', "l1"], function (err, reply) {
    console.log("Queue Length", reply);
});
```

```
redisClient.rpush(['test-key', "l1", "l2"], function (err, reply) {
    console.log("Queue Length", reply);
});
```

**lrange method -** 
We can retrieve list of data from this method, starting index would be 0.

This will give 1st left value, since we have passed end value as 0 -

```
redisClient.lrange('test-key', 0, 0, function (err, reply) {
    console.log("Queue result", reply);
});
```

This will give 2 left values, since we have passed end value as 1 -

```
redisClient.lrange('test-key', 0, 1, function (err, reply) {
    console.log("Queue result", reply);
});
```

This will give all values of key, since we have passed end value as -1 -

```
redisClient.lrange('test-key', 0, -1, function (err, reply) {
    console.log("Queue result", reply);
});
```

**lpop method -** 
This will delete most left element from the queue - 

```
redisClient.lpop(['test-key'], function (err, reply) {
    console.log("Popped item", reply);
});
```

## Conclusion
This way we can maintain queue in same redis-key. There is another methods available set(to set value for key), get(get value for key), del (delete key), rpop. 
