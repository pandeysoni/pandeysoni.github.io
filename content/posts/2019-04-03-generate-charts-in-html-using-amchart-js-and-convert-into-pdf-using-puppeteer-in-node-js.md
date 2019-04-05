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

[Amchart.js](https://www.amcharts.com/) is a complete package to generate charts and they have given option to customize more according to our need. We'll take example of serial chart and How we have used more options provided by Amchart.js. 

Note - They have latest version 4, but here we have used version 3.

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
5. Placed images before app name in y-axis.

Please take a look at the below piece of code -
```js
function getNumberSeparators() {

    // default
    var res = {
        "decimal": ".",
        "thousand": ""
    };

    // convert a number formatted according to locale
    var str = parseFloat(1234.56).toLocaleString();

    // if the resulting number does not contain previous number
    // (i.e. in some Arabic formats), return defaults
    if (!str.match("1"))
        return res;

    // get decimal and thousand separators
    res.decimal = str.replace(/.*4(.*)5.*/, "$1");
    res.thousand = str.replace(/.*1(.*)2.*/, "$1");

    // return results
    return res;
}
var separators = getNumberSeparators();

var uniqueUsersChart = AmCharts.makeChart("uniqueUsersChart", {
    "addClassNames": true,
    "type": "serial",
    "theme": "light",
    "decimalSeparator": separators.decimal,
    "thousandsSeparator": separators.thousand,
    "categoryField": "app",
    "rotate": true,
    "fontFamily": "Lato",
    "categoryAxis": {
        "position": "left",
        "title": "List of Applications",
        "fontSize": 30,
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
            "fontSize": 27,
            "color": "#294E79",
            "titleColor": "#294E79",
            "titleFontSize": 27,
            "showLastLabel": false,
            "labelFunction": function (value) {
                return numberFormatter(value);
            }
        }
    ],
    "dataProvider": [{ "app": "Quora", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 16 }, { "app": "Bcbe learn", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 13 }, { "app": "Wikipedia", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 10 }, { "app": "XtraMath", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 2 }, { "app": "Absolute", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 5 }, { "app": "iPrism Web filt...", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 5 }, { "app": "Amazon", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 11 }, { "app": "YouTube", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 20 }, { "app": "Lynda", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 4 }, { "app": "ABCya1", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 6 }],
    "startDuration": 0
})
var uniqueUsersDataArray = [{ "app": "Quora", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 16 }, { "app": "Bcbe learn", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 13 }, { "app": "Wikipedia", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 10 }, { "app": "XtraMath", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 2 }, { "app": "Absolute", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 5 }, { "app": "iPrism Web filt...", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 5 }, { "app": "Amazon", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 11 }, { "app": "YouTube", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 20 }, { "app": "Lynda", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 4 }, { "app": "ABCya1", "image": "https://qphs2.fs.quoracdn.net/main-qimg-bba34c5494cd15bbe9ba9c29d47740be.webp", "usersCount": 6 }];
function numberFormatter(value) {
    // return num > 999 ? (num/1000).toFixed(1) + 'k' : num
    if (value < 1000) {
        return value;
    }

    if (value >= 1000 && value <= 1000000) {
        return Math.round(value / 1000) + 'k';
    }

    if (value >= 1000000 && value <= 1000000000) {
        return Math.round(value / 1000000) + 'M';
    }

    if (value >= 1000000000 && value <= 1000000000000) {
        return Math.round(value / 1000000000) + 'B';
    }

    else {
        return Math.round(value / 1000000000000) + 'T';
    }
}
const uniqueAppLength = uniqueUsersDataArray.filter(d => d.app && d.app.length > 10);
console.log("Unique user")
if (uniqueAppLength.length > 0) {
    uniqueUsersChart.categoryAxis.labelOffset = 240
}
else {
    uniqueUsersChart.categoryAxis.labelOffset = 200
}
function modifyAxis(e) {
    var axes = e.chart.valueAxes;

    for (i1 in axes) {
        var labels = axes[i1].chart.categoryAxis.allLabels;
        var parent = labels[0].node.parentNode;
        const app_10_Length = uniqueUsersDataArray.filter(d => d.app && d.app.length > 13);
        if (app_10_Length.length > 0) {
            parent.childNodes[uniqueUsersDataArray.length].childNodes[0].setAttribute("y", "150");
        }
        else if (uniqueAppLength.length > 0) {
            parent.childNodes[uniqueUsersDataArray.length].childNodes[0].setAttribute("y", "110");
        }
        else {
            parent.childNodes[uniqueUsersDataArray.length].childNodes[0].setAttribute("y", "90");
        }
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
## 2. Puppeteer -

We have used puppeteer to generate above chart into pdf.  Let's take a look to index.js file which take html as an input and pdfConverter.js file which convert html file into pdf.
