---
template: post
title: >-
  Generate Charts in HTML using Amchart.js and convert into PDF using Puppeteer
  in Node.js
slug: >-
  /generate-charts-in-html-using-amchart.js-and-convert-into-pdf-using-puppeteer-in-node
draft: false
date: 2019-04-10T12:07:00.000Z
description: >-
  Learn how to create charts using Amchart.js and customize it in HTML file and
  convert that file into PDF using puppeteer.
category: Node.js
tags:
  - Node.js
  - Pdf
  - Puppeteer
  - Amchart.js
  - Charts
---
Recently, I came across the popular site which is famous for Node.js blogging and they host a blog on [Generating PDF from HTML with Node.js and Puppeteer](https://blog.risingstack.com/pdf-from-html-node-js-puppeteer/). This blog talks about the challenges which a Node.js developer face during Pdf generation. We also faced the similar issue while generating pdf eight months ago and problem was solved using puppetter library. There was one more challenge, where we had to generate dynamic report with some analytic data. 

![Dynamic chart in Pdf](/media/screen-shot-2019-04-10-at-7.25.01-am.png)

In this article We'll cover below points -

1. Amchart.js module to generate charts.
2. Convert ejs to HTML file.
3. Puppeteer library to generate PDF.

## 1. Amchart.js -

[Amchart.js](https://www.amcharts.com/) is a complete package to generate charts and they have given option to customize more according to our need. We'll take example of serial chart and how we have used more options provided by Amchart.js.  

**_Note - They have latest version 4, but we have used version 3 as we have already implemented in same version. Implementation can be different for other versions._**

**Serial Chart** - This is the basic example provided by Amchart.js for serial chart.

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
5. Added images before app name in y-axis.

Please take a look at the below piece of code -

```js
var uniqueUsersChart = AmCharts.makeChart("uniqueUsersChart", {
			"addClassNames": true,
			"type": "serial",
			"theme": "light",
			"categoryField": "app",
			"rotate": true,
			"fontFamily": "Lato",
			"categoryAxis": {
				"position": "left",
				"title": "List of Applications",
				"fontSize": 18,
				"color": "#294E79",
				"titleColor": "#294E79",
				 "titleFontSize": 30
			},
			"graphs": [{
				"type": "column",
				"id": "AmGraph-1",
				"lineAlpha": 0,
				"type": "column",
				"valueField": "usersCount",
				"fillColors": ["#9FCFFD", "#136CE5"],
				"fixedColumnWidth": 15,
				"fillAlphas": 0.8,
				"gradientOrientation": "horizontal"
			}],
			"valueAxes": [
				{
					"id": "ValueAxis-1",
					"position": "bottom",
					"gridPosition": "start",
					"title": "Number of Users",
					"fontSize": 18,
					"color": "#294E79",
					"titleColor": "#294E79",
					"titleFontSize": 27,
					"showLastLabel":false,
					"labelFunction": function(value) {
						return numberFormatter(value);
					}
				}
			],
			"dataProvider": [{"app":"Quora","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":16},{"app":"Bcbe learn","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":13},{"app":"Wikipedia","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":10},{"app":"XtraMath","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":2},{"app":"Absolute","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":5},{"app":"iPrism Web filt...","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":5},{"app":"Amazon","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":11},{"app":"YouTube","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":20},{"app":"Lynda","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":4},{"app":"ABCya1","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":6}],
			"startDuration": 0
		})
		var uniqueUsersDataArray = [{"app":"Quora","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":16},{"app":"Bcbe learn","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":13},{"app":"Wikipedia","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":10},{"app":"XtraMath","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":2},{"app":"Absolute","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":5},{"app":"iPrism Web filt...","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":5},{"app":"Amazon","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":11},{"app":"YouTube","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":20},{"app":"Lynda","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":4},{"app":"ABCya1","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":6}];
		function numberFormatter(value) {
			// return num > 999 ? (num/1000).toFixed(1) + 'k' : num
			if (value < 1000) {
				return value;   
			}
			
			if (value >= 1000 && value <= 1000000) {
				return  Math.round(value/1000) + 'k';   
			}
			
			if (value >= 1000000 && value <= 1000000000) {
				return Math.round(value/1000000) + 'M';   
			}
			
			if (value >= 1000000000 && value <= 1000000000000) {
				return Math.round(value/1000000000) + 'B';   
			}
			
			else {
				return Math.round(value/1000000000000) + 'T';   
			}
		}
		const uniqueAppLength = uniqueUsersDataArray.filter(d => d.app && d.app.length > 10);
		uniqueUsersChart.categoryAxis.labelOffset = 120
		function modifyAxis(e) {
		
			var axes = e.chart.valueAxes;

			for (i1 in axes) {
			var labels = axes[i1].chart.categoryAxis.allLabels;
				var parent = labels[0].node.parentNode;
				const app_10_Length = uniqueUsersDataArray.filter(d => d.app && d.app.length > 13);
				parent.childNodes[uniqueUsersDataArray.length].childNodes[0].setAttribute("y", "60");
				
				parent.childNodes[uniqueUsersDataArray.length].setAttribute("class", "amcharts-main-div-text");
				for (i2 in labels) {
					var label = labels[i2].node;
					labels[i2].node.setAttribute("text-anchor", "start")
					var group = document.createElementNS('http://www.w3.org/2000/svg', "g");
					var img = document.createElementNS('http://www.w3.org/2000/svg', "image");
					// Setup image
					img.setAttributeNS('http://www.w3.org/1999/xlink', 'href', uniqueUsersDataArray[i2].image);
					img.setAttribute('x', '-50');
					img.setAttribute('y', '-11'); // half the height
					img.setAttribute('width', '40');
					img.setAttribute('height', '40');
					// Swap position to group; remove from label
					group.setAttribute('transform', label.getAttribute('transform'));
					label.setAttribute('transform', '');

					// Group axis labels
					group.appendChild(label);
					group.appendChild(img);
					parent.appendChild(group);
				}
			}
		}
		
		uniqueUsersChart.addListener("drawn", modifyAxis);
```

## 2. Generate Dynamic chart using ejs

There is not much difference into ejs and htlm file. Please click on the link to quick look up - https://ejs.co. \
\
Here, we are taking ejs file as an input with some dynamic value and convert into HTML file. Please look at the below piece of code - 

```js
const data = {uniqueUsersDataArray:[{"app":"Quora","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":16},
                                    {"app":"Bcbe learn","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":13},
                                    {"app":"Wikipedia","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":10},
                                    {"app":"XtraMath","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":2},
                                    {"app":"Absolute","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":5},
                                    {"app":"iPrism Web filt...","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":5},
                                    {"app":"Amazon","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":11},
                                    {"app":"YouTube","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":20},
                                    {"app":"Lynda","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":4},
                                    {"app":"ABCya1","image":"https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp","usersCount":6}]} 

const filePathName = path.resolve(__dirname, 'filename.ejs');
const htmlString = fs.readFileSync(filePathName).toString();
const template = ejs.compile(htmlString);
return template(data); //data which you want to put dynamically in ejs like dataprovider values in charts.
```

## 3. Puppeteer -

We have used puppeteer**_ version -"^1.6.2"_** to generate above chart into pdf.  Let's take a look to index.js file which take html as an input and pdfConverter.js file which convert html file into pdf. Implementation can be different for other versions.

**index.js**

```js
const tmp = require('tmp');
const fs = require('fs');
const pdfConverter = require('./pdfConverter');
function htmlToPdf() {
  (async () => {
    try {
      const htmlData = fs.readFileSync('index.html').toString();
      await pdfConverter.convert_html_string_to_pdf(htmlData);
    } catch (err) {
      console.log("Error processing request: " + err);
    }
  })()
}
htmlToPdf();
```

pdfConverter.js - We have added footer to the page in footerTemplate, for this displayHeaderFooter should be true in pdf method.

```js
const puppeteer = require('puppeteer');

async function convert_html_string_to_pdf(html_string) {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();

    page.on('requestfailed', request => {
    })
    page.on('requestfinished', request => {
        resp = request.response()
    })
   
    await page.goto('data:text/html,' + html_string, { waitUntil: 'networkidle0' });
    
    await page.pdf({
        path: 'index.pdf', format: 'A4', printBackground: true, displayHeaderFooter: true,
        footerTemplate: `<style>@font-face{font-family:Lato;src:url('http://fonts.googleapis.com/css?family=Lato&subset=latin,latin-ext') font-style:Regular} )}h1,h2,h3,h4,h5,h6,p,span{font-family:Lato}</style><p style="-webkit-print-color-adjust: exact;color:#294E79;font-size: 7pt; text-align:center; margin-left:225px;">Copyright © thepandeysoni.org 2019</p>`,
        margin: {
            bottom: "50px"
          }
       });
    console.log("File has been saved as mentioned path.")
    await browser.close()
}


exports.convert_html_string_to_pdf = convert_html_string_to_pdf;
```

Here is the [source code](https://github.com/pandeysoni/Generating-Charts-in-PDF-in-node.js), where you'll be able to see index.html and index.pdf file.
