---
template: post
title: >-
  Generate Charts in HTML using Amchart.js and convert into PDF using Puppeteer
  in Node.js
slug: >-
  /generate-charts-in-html-using-amchart.js-and-convert-into-pdf-using-puppeteer-in-node
draft: true
date: 2019-04-03T12:07:42.317Z
description: >-
  Learn how to create charts using Amchart.js and customize it in HTML file and
  convert that file into PDF using puppeteer.
category: Node.js
tags:
  - Node.js
  - PDF
  - Puppeteer
  - Amchart.js
  - Charts
---
This is one of challenging thing I have done in past, where I have to generate report with some analytic data, so the challenge was to generate charts and setting up those charts to PDF in standard format. 

Here we'll learn about - 

1. Amchart.js module to generate charts.
2. Puppeteer library to generate PDF.

## 1. Amchart.js -

[Amchart.js](https://www.amcharts.com/) is a complete package to generate charts and they have given option to customize more according to our need. We'll take example of serial and pie chart and How we have used more options provided by Amchart.js. 

Note - They have latest version 4, but here we have used version 3.

**i) Serail Chart** - This is the basic example provided by Amchart.js for serial chart.

```js
var uniqueUsersChart = AmCharts.makeChart("uniqueUsersChart", {
			"addClassNames": true,
			"type": "serial",
			"theme": "light",
			"categoryField": "app",
			"rotate": true,
			"categoryAxis": {
				"position": "left",
				"title": "List of Applications"
			},
			"graphs": [{
				"type": "column",
				"id": "AmGraph-1",
				"lineAlpha": 0,
				"type": "column",
				"valueField": "usersCount",
				"fillColors": ["#9FCFFD"],
				"fixedColumnWidth": 30,
				"fillAlphas": 1
			}],
			"valueAxes": [
				{
					"id": "ValueAxis-1",
					"position": "bottom",
					"gridPosition": "start",
					"title": "Number of Users"
				}
			],
			"dataProvider":[{"app":"Quora","usersCount":800000},
                            {"app":"Gmail","usersCount":1200000},
                            {"app":"Facebook","usersCount":1600000},
                            {"app":"Instagram","usersCount":2100000},
                            {"app":"YouTube","usersCount":2233568}],
			"startDuration": 1
		})
```

Please find the complete code [link](https://codepen.io/pandeysoni/pen/LvVyMK).

The following changes made to above chart -

1. Number formatting.

2. Font Size.

3. Font Style.

4. Animation off while loading chart.

5. Placed images before app name in y-axis.
