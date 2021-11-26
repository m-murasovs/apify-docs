---
title: Saving results to CSV
description: Learn how to save the results of your scraper to a CSV file.
menuWeight: 20.8
paths:
    - data_collection_fundamentals/save-to-csv
---

# [](#saving-to-csv) Saving results as a CSV

In the last chapter we were able extract data about all the websites from the [Alexa Top Sites index](https://www.alexa.com/topsites). That's great. But we ended up with results printed to the terminal, which is not very useful for further processing. In this chapter we'll learn how to save that data into a CSV file which you can then open in Excel or Google Sheets.

## [](#converting-to-csv) Converting to CSV

It might look like a big programming challenge to transform a JavaScript object into a CSV, but thanks to NPM, it will be a piece of cake. After googling `json to csv npm` we found that there's a library called [`json2csv`](https://www.npmjs.com/package/json2csv) that can convert a JavaScript object to a CSV with a single function call. Perfect.

First we need to import the `parse()` function from the library.

```js
import { parse } from 'json2csv';
```

and then we need to parse the `results` from the previous chapter.

```js
const csv = parse(results);
```

The full code including the earlier scraping part now looks like this.

```js
// main.js
import { gotScraping } from 'got-scraping';
import cheerio from 'cheerio';
import { parse } from 'json2csv';

const response = await gotScraping('https://www.alexa.com/topsites');
const html = response.body;

const $ = cheerio.load(html);
const sites = $('div.site-listing').toArray();
const results = sites.map((site) => {
    const fields = $(site).find('div.td');
    return {
        rank: fields.eq(0).text().trim(),
        site: fields.eq(1).text().trim(),
        dailyTimeOnSite: fields.eq(2).text().trim(),
        dailyPageViews: fields.eq(3).text().trim(),
        percentFromSearch: fields.eq(4).text().trim(),
        totalLinkingSites: fields.eq(5).text().trim(),
    };
});

const csv = parse(results);
console.log(csv);
```

And here's our newly created CSV printed to the console after running the script.

![Printing CSV data to terminal]({{@asset data_collection_fundamentals/images/terminal-csv.webp}})

## [](#writing-to-file) Writing the CSV to a file

The final task that remains is to save our CSV formatted data to a file on our disk, so we can open it or send it to someone. For this we don't need any extra NPM packages, because functions for saving files are included in Node.js.

First we import the `writeFileSync` function from the `fs` (file system) package.

```js
import { writeFileSync } from 'fs';
```

and then we call it with a file name and the CSV data.

```js
writeFileSync('alexa-websites.csv', csv);
```

When we complete the code, it looks like this.

```js
// main.js
import { gotScraping } from 'got-scraping';
import cheerio from 'cheerio';
import { parse } from 'json2csv';
import { writeFileSync } from 'fs';

const response = await gotScraping('https://www.alexa.com/topsites');
const html = response.body;

const $ = cheerio.load(html);
const sites = $('div.site-listing').toArray();
const results = sites.map((site) => {
    const fields = $(site).find('div.td');
    return {
        rank: fields.eq(0).text().trim(),
        site: fields.eq(1).text().trim(),
        dailyTimeOnSite: fields.eq(2).text().trim(),
        dailyPageViews: fields.eq(3).text().trim(),
        percentFromSearch: fields.eq(4).text().trim(),
        totalLinkingSites: fields.eq(5).text().trim(),
    };
});

const csv = parse(results);
writeFileSync('alexa-websites.csv', csv);
```

Finally, after running it again, we will find the `alexa-websites.csv` file in our project folder. And when we open it with Excel, voila.

![Displaying CSV data in Excel]({{@asset data_collection_fundamentals/images/data-in-excel.webp}})

This marks the end of the Fundamentals of data extraction section of the Web Scraping Academy. If you enjoyed the tutorial, give us a thumbs up down below and if you're eager to learn more...

## [](#next) Next up

Next up are the Fundamentals of crawling, where we will learn how to move between web pages and scrape data from all of them. We will build a scraper that first collects all the countries of the [Alexa Top Sites by Country index](https://www.alexa.com/topsites/countries) and then crawls each of them to scrape the data for each country separately.