---
template: post
title: Scrape website using puppeteer in node.js
slug: /2019/09/06/scrape-website-using-puppeteer
draft: true
date: 2019-09-06T06:49:58.380Z
description: How to scrape a website using puppeteer in node.js
category: 'Node.js, Puppeteer'
tags:
  - Scrape
  - Puppeteer
  - node.js
  - scrapper
  - best scrapper
---
## **Overview**

[Puppeteer ](https://pptr.dev/)is a headless chrome Node API. 

Puppeteer can be used for different purposes -

1. Generate pdf from html page. 
2. Scrape website data in json.
3. Capture screenshots in pdf or image format of a website. 
4. Performance testing of a website.

In last [article](https://thepandeysoni.org/generate-charts-in-html-using-amchart.js-and-convert-into-pdf-using-puppeteer-in-node) gone through the steps that how can we generate pdf of any html page and can be used for report purposes.

Here we'll cover 2nd point, how can we extract the website data and map into json format.

![](/media/untitled-diagram-6-.jpg)

## Prerequisite

1. Javascript knowledge
2. Node.js installed (version 8.10 and above)

## How to scrape website data and map into json

Here I'll scrape google search website data content -

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

## Conclusion
