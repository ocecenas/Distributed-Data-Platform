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

## Query Service (Microsoft Power Query)

Developing a query to merge Agile GPN information with the parametric data retrieved from scraping can be done with Microsoft Power Query ([Power Query Documentation](https://learn.microsoft.com/en-us/powerquery-m/power-query-m-function-reference)). You may also use SQL to write the query to merge GPN information with the scraped parametric data.

Use Get Data option under the Data Tab in Microsoft Excel to upload the JSON file into Excel. 


Query for Joining & Merging Relevant Parametrics on the Parametric Data retrieved from SiliconExpert:

    let
        Source = Excel.Workbook(File.Contents("Inductors\SE_Export.xlsx"), null, true),
        #"Sorted Rows" = Table.Sort(Source,{{"Name", Order.Ascending}}),
        #"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each ([Kind] <> "Sheet")),
        se_data_final_Table = #"Filtered Rows"{[Item="se_data_final",Kind="Table"]}[Data],
        #"Changed Type" = Table.TransformColumnTypes(se_data_final_Table,{{"Part Number", type text}, {"Description", type text}, {"Subclass", type text}, {"Mfr Name", type text}, {"MPN", type text}, {"MPN From Site", type text}, {"Link", type text}, {"Match", type text}, {"Current - RMS (mA)", type number}, {"DC Resistance - Max (Ohms)", type number}, {"DC Current - Max (mA)", type number}, {"Saturation Current - Max (mA)", type number}, {"Self-Resonant Frequency - Min (Hz)", type number}, {"Quality Factor - Min", Int64.Type}, {"Quality Test Frequency (Hz)", Int64.Type}, {"Package Size (mm2)", type number}, {"Product Height (mm)", type number}, {"Package Type", type text}, {"Length", type number}, {"Width", type number}, {"Height", type number}}),
        #"Added Custom" = Table.AddColumn(#"Changed Type", "Source", each "Silicon Expert")
    in
        #"Added Custom"

Query for Joining & Merging Relevant Parametrics on the Parametric Data retrieved directly from the Manufacturer Website:

    let
        Source = Excel.Workbook(File.Contents("Inductors\mur_coilcraft_tdk1.xlsx"), null, true),
        SelectMfrTbl_Append_Table = Source{[Item="SelectMfrTbl_Append",Kind="Table"]}[Data],
        #"Changed Type" = Table.TransformColumnTypes(SelectMfrTbl_Append_Table,{{"Part Number", type text}, {"Description", type text}, {"Subclass", type text}, {"Mfr Name", type text}, {"MPN", type text}, {"MPN From Site", type text}, {"Link", type text}, {"Match", type text}, {"Current - RMS (mA)", Int64.Type}, {"DC Resistance - Max (Ohms)", type number}, {"DC Current - Max (mA)", type any}, {"Saturation Current - Max (mA)", Int64.Type}, {"Self-Resonant Frequency - Min (Hz)", Int64.Type}, {"Quality Factor - Min", Int64.Type}, {"Quality Test Frequency (Hz)", Int64.Type}, {"Package Size (mm2)", type number}, {"Product Height (mm)", type number}, {"Package Type", type any}, {"Length", type number}, {"Width", type number}, {"Height", type number}}),
        #"Added Custom" = Table.AddColumn(#"Changed Type", "Source", each "Select Mfr")
    in
        #"Added Custom"
