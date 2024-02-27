## Web Scraper Continued (Puppeteer / Node.js)

### Scrape Parametrics from Part Listing Page

Use the Developer tools to find the selectors for the parametric data. Typically, the selectors will be table rows ```<tr>```, paragraphs ```<p>```, span ```<span>```, and anchor tags ```<a>```. The puppeteer functions ```page.evaluate()``` and ```page.$eval()``` can be used to run javascript commands to manipulate HTML code. The following function is an example of puppeteer retrieving parametric data from the silicon expert part listing:

    const getMPNParams = async(page, row_element) => await page.evaluate(row_element_input => {
        let item = {param_name: null, param_val: null}
        item.param_name = row_element_input.querySelector(`div.col.key`).innerText.replace(/(\r\n|\n|\r)/gm, "").trim()
        item.param_val = row_element_input.querySelector(`div.col.val`).innerText.replace(/(\r\n|\n|\r)/gm, "").trim()

        return item
    }, row_element)

### Write the Parametric Data to a JSON file

Part data should frequently be written to a JSON file, so that data will not be lost if an error were to occur. The JSON file will later be used to uploaded to a database or loaded into Excel. A function to write an array of objects to a JSON file is listed below:

    function saveJSON(filename, obj_arr) {
        let content = '[\r\n' + obj_arr.map(item => JSON.stringify(item)).join(',\r\n') + '\r\n]'
    
        if (fs.existsSync(filename)) {
        fs.truncate(filename, 0, function() { console.log('done') })
        fs.writeFile(filename, content, {encoding:'utf8',flag: 'a+'}, err => {}); 
        } else {
        fs.writeFile(filename, content, {encoding:'utf8',flag:'w+'}, err => {}); 
        }
    }

## Query Service

Developing a query to merge Agile GPN information with the parametric data retrieved from scraping can be done by either using SQL or Microsoft Power Query.
