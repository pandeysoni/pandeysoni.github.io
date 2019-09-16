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

There was one requirement, where we had to process the data in sequential manner one after another, then we thought about queue mechanism. The challenge was that we were not able to find how to write redis methods (lrange, lpop, lpush) in Node.js. Here, we have written redis commands (lrange, lpop, lpush) in node.js and that worked pretty well for us. Let's take a look to these methods in Node.js - 

## Get Started

First we'll init package file and then will install redis package there -

```
mkdir redis-app
cd redis-app
npm init
npm install --save redis
```

Now, create new file in redis-app folder with the name of index.js, there we'll try to connect with redis-server (index.js) - 

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

We have to process the data in sequential manner one after another in one key and one key's value will have complete list. We'll push the data from right side and pop the data from left side and get the left most data from list. Here we'll use lrange, lpop, rpush method of redis to perofrm this queue mechanism. Please take a look at below image for more clarification - 

![](/media/untitled-diagram-7-.jpg)

**rpush method -** 
rpush method allows us to push one or multiple items to redis key. _Here, our key name is 'test-key'._

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
lrange method allows us to retrieve list of data from this method, with starting index 0.

This will give the left most value of the key 'test-key', since we have passed start and end value as 0.

```
redisClient.lrange('test-key', 0, 0, function (err, reply) {
    console.log("Queue result", reply);
});
```

Now, we will get 2 values from the left of the key 'test-key', since we have passed start value as 0 and end value as 1.

```
redisClient.lrange('test-key', 0, 1, function (err, reply) {
    console.log("Queue result", reply);
});
```

This will give all values of key, since we have passed start value as 0 and end value as -1.

```
redisClient.lrange('test-key', 0, -1, function (err, reply) {
    console.log("Queue result", reply);
});
```

**lpop method -** 
lpop method allows us to delete most left element from the queue - 

```
redisClient.lpop(['test-key'], function (err, reply) {
    console.log("Popped item", reply);
});
```

The same way, there is rpop method available which will work in a similar way as lpop, the only difference would be this will pop out the data from right side in place of left side from the queue list.

## Conclusion

This way we have maintained queue in redis. There is another methods available set(to set value for key), get(get value for key), del (delete key) and many more.
