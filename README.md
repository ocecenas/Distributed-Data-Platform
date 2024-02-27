# Distributed Data Processing for Approved Manufacturer Parts (Guide)

## Overview

This distributed data processing guide will show the user how to retrieve parametric data for selected Manufacturer Parts. It will retrieve parametric data for a selected part by using a crawler to search for matches on the Manufacturer/Supplier Website. No frameworks will be used to faciliate the data processing (ex. Apache Spark or Apache Airflow). SiliconExpert will be used as an example in this guide.

Steps:
1. Load the parts from the database or file
2. Initialize the Web Driver
3. Search the Parts on the Manufacturer/Supplier Search Engine
4. Retrieve Parametric Data from the part listing page.
5. Upload the data to a database or write to file
6. Run a query service on the datasets that filters duplicates and merges GPN records with the parametric data

The programming language used in this guide will be Node.js ([Node.js API](https://nodejs.org/docs/latest/api/)) and the crawler used will be Puppeteer ([Puppeteer API](https://pptr.dev/api/puppeteer.elementhandle)). 

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

In order to scrape the parametric data for a selected part, you first must search and go to the part listing on the manufacturer/supplier website. Use the developer tools feature on your browser to find the selectors for the search fields, buttons, links, and text. Use the ```page.type(selector, input_text)``` function to enter the Part Number into the field and the ```page.click(selector)``` function to click the search button.

Retrieve part listing from the search results by selecting the result with the shortest [levenstein distance](https://www.npmjs.com/package/fast-levenshtein). 

The example below is a function to search for part on SiliconExpert:

    async function searchMPN(page, mpn) {
        const myDelay = async (ms) => {return await new Promise(resolve => setTimeout(resolve, ms))}

        const searchFldCSS = 'input[id="input-searchbox"]'; // Selector for search field
        const searchBtnCSS = 'button[id="header-main-searchbox-button"]'; // Selector for search button


        // Wait for the page to load and enter the MPN into the search field
        await page.waitForSelector(searchFldCSS)
        await page.focus(searchFldCSS);
        await page.type(searchFldCSS, mpn);
        await myDelay(1000); // wait 1 second

        // Click the search button and wait for search results
        await page.focus(searchBtnCSS)
        await page.click(searchBtnCSS)
        await myDelay(3000); // wait 3 second

        await page.$eval(searchFldCSS, (ele) => {ele.value = ''}) // Clear the search field

        const result_banner = `div[class="wrapper n_results_for"]` // Selector for the search results banner
        let obj = {startTime: null, timeout: 5000, flag: mpn} // Object to pass to waitUntilFlag function
        await waitUntilFlag(read_page, obj, page, result_banner) // Wait until the search results banner is displayed
    }



## Query Service

Developing a query to merge Agile GPN information with the parametric data retrieved from scraping can be done by either using SQL or Microsoft Power Query.
