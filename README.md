# Distributed Data Processing

## Overview

This distributed data processing method will retrieve parametric data for selected Manufacturer Parts. It will retrieve parametric data for a selected part by using a crawler to search for matches on the Manufacturer/Supplier Website. No frameworks will be used to faciliate the data processing (ex. Apache Spark or Apache Airflow).

![image](https://github.com/ocecenas/Distributed-Data-Platform/assets/46056159/b0f4aff1-d2b7-44d3-aea2-790b0270fac5)


## Prerequisites
- Visual Studio
- WebDriver: Puppeteer, Selenium, or Microsoft Edge WebDriver
- .NET, NodeJS, or Python

## Load the Parts
Load the MPNs from a file or database, ensure that the record is related to the GPN and there is a specified Manufacturer and Manufacturer Part Number. 

## Web Scraper Deployment (Puppeteer / Node.js)

### Initialize the Web Driver

Begin a Puppeteer Session by entering the following code. Configuring the browser with headless off will make debugging the program easier.

    const puppeteer = require('puppeteer')

    const options = {width: 1800, height: 1080};
    const browser = await puppeteer.launch({
        headless: false,
        executablePath: 'C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe',
        args: [`--window-size=${options.width},${options.height}`], // new option
    })

    const page = await browser.newPage();
    await page.setViewport({ width: options.width,height: options.height });
    await page.goto(URL, {waitUntil:'networkidle2'});



### Search the Parts on the Manufacturer/Supplier Search Engine

Visit a select manufacturer/supplier website (login if necessary) and feed the manufacturer parts into their search engine. 

### Select Part from the Results Page by Closest Match

### Scrape the Parametrics from the Part Page

## Query Service

Developing a query to merge Agile GPN information with the parametric data retrieved from scraping can be done by either using SQL or Microsoft Power Query.
