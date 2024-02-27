# Distributed-Data-Platform

## Overview
![image](https://github.com/ocecenas/Distributed-Data-Platform/assets/46056159/cb43ce70-28d2-4944-a484-0bd62dc94716)

## Prerequisites
- Visual Studio
- WebDriver: Puppeteer, Selenium, or Microsoft Edge WebDriver
- .NET, NodeJS, or Python

## Load the Parts
Load the MPNs from a file or database, ensure that the record is related to the GPN and there is a specified Manufacturer and Manufacturer Part Number. 

## Web scraper Deployment (Puppeteer use in Node.js)

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

### Select Part from the Results Page by Closest Match

### Scrape the Parametrics from the Part Page

## Query Service

Developing a query to merge Agile GPN information with the parametric data retrieved from scraping can be done by either using SQL or Microsoft Power Query.
