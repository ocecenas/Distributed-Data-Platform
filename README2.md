## Web Scraper Continued (Puppeteer / Node.js)

### Scrape Parametrics from Part Listing Page

Use the Developer tools to find the selectors for the parametric data. Typically, the selectors will be table rows ```<tr>```, paragraphs ```<p>```, span ```<span>```, and anchor tags ```<a>```. The puppeteer functions ```page.evaluate()``` and ```page.$eval()``` can be used to run javascript commands to manipulate HTML code. The following function is an example of puppeteer retrieving parametric data from the silicon expert part listing:

    const getMPNParams = async(page, row_element) => await page.evaluate(row_element_input => {
        let item = {param_name: null, param_val: null}
        item.param_name = row_element_input.querySelector(`div.col.key`).innerText.replace(/(\r\n|\n|\r)/gm, "").trim()
        item.param_val = row_element_input.querySelector(`div.col.val`).innerText.replace(/(\r\n|\n|\r)/gm, "").trim()

        return item
    }, row_element)

## Query Service

Developing a query to merge Agile GPN information with the parametric data retrieved from scraping can be done by either using SQL or Microsoft Power Query.
