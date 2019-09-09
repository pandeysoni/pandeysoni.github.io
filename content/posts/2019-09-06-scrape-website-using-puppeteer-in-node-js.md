---
template: post
title: Scrape website using puppeteer in node.js
slug: /2019/09/09/scrape-website-using-puppeteer
draft: false
date: 2019-09-09T06:49:00.000Z
description: How to scrape a website using Puppeteer in node.js
category: 'Node.js, Puppeteer'
tags:
  - Scrappere
  - Puppeteer
  - node.js
---
## **Overview**

[Puppeteer ](https://pptr.dev/)is a headless chrome Node API. 

Puppeteer can be used for different purposes -

1. Generate pdf from html page. 
2. Scrape website data in json.
3. Capture screenshots in pdf or image format of a website. 
4. Performance testing of a website.

In the last [article ](https://thepandeysoni.org/generate-charts-in-html-using-amchart.js-and-convert-into-pdf-using-puppeteer-in-node)gone through the steps that how can we generate pdf of any HTML page and can be used for report purposes.

Here we'll cover 2nd point, how we can extract the website data and map the extracted data into JSON format.

![](/media/untitled-diagram-6-.jpg)

## Prerequisite

1. Javascript knowledge
2. Node.js installed (version 8.10 and above)

## Get Started

Here We'll scrape google search website data content -\
NOTE - The output list could be vary.

```
const puppeteer = require('puppeteer');
try {
    (async () => {
        /** by default puppeteer launch method have headless option true*/
        const browser = await puppeteer.launch({
            headless: false
        });
        const page = await browser.newPage();
        await page.goto('https://www.google.com/');
        await page.type('input[aria-label="Search"]', 'node js tutorial');
        await page.keyboard.press('Enter');
        
        /** waitfor while loding the page, otherwise evaulate method will get failed. */
        await page.waitFor(5000);
        const list = await page.evaluate(() => {
            let data = []

            /** this can be changed for other website.*/
            const list = document.querySelectorAll('.rc .r');
            for (const a of list) {
                data.push({
                    'title': a.querySelector('.LC20lb').innerText.trim().replace(/(\r\n|\n|\r)/gm, " "),
                    'link': a.querySelector('a').href
                })
            }
            return data;
        })
        console.log(list);
        await browser.close();
    })()
} catch (err) {
    console.error(err);
}
```

The output json will look like this -

```
[ { title: 'Node.js Tutorial - W3Schools',
    link: 'https://www.w3schools.com/nodejs/' },
  { title: 'Node.js Tutorial - Tutorialspoint',
    link: 'https://www.tutorialspoint.com/nodejs/index.htm' },
  { title: 'Node.js Tutorial: Learn in 3 Days - Guru99',
    link: 'https://www.guru99.com/node-js-tutorial.html' },
  { title: 'Learn Node.js Tutorial - javaTpoint',
    link: 'https://www.javatpoint.com/nodejs-tutorial' },
  { title: 'Node.js Tutorial | All You Need To Know About Node.js ...',
    link: 'https://www.edureka.co/blog/nodejs-tutorial/' },
  { title: 'Node.js Tutorials - For beginners and professionals',
    link: 'https://www.tutorialsteacher.com/nodejs/nodejs-tutorials' },
  { title: 'Learn Node.js | Codecademy',
    link: 'https://www.codecademy.com/learn/learn-node-js' },
  { title:
     'Learn Node.js: Tutorials for Programmers of All Levels - Stackify',
    link: 'https://stackify.com/learn-nodejs-tutorials/' },
  { title: '',
    link:
     'https://medium.com/quick-code/top-10-reason-why-you-should-use-node-js-5926b4d1f2c1' },
  { title: '',
    link:
     'https://hostpresto.com/blog/should-i-learn-django-or-node-js/' },
  { title: '',
    link: 'https://www.tutorialspoint.com/nodejs/nodejs_introduction' },
  { title: '',
    link:
     'https://www.quora.com/What-are-the-best-resources-for-learning-Node-js' } ]
```

You can prepare JSON as per your need.

## Conclusion

There is other modules (cheerio, cypress etc.) available for scrapping. Puppeteer is one good option for scrapping the data and we can actually observe the website and map the data into JSON.
