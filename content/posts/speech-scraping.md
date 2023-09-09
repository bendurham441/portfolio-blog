---
title: "Fed NLP: Data Collection Part 1"
date: 2023-09-09T13:11:04-04:00
tags: []
draft: false
description: 'As a first step in my analysis of Federal Reserve speeches using Natural Language Processing (NLP) techniques, I document the process of gathering Federal Reserve speeches from the Bank for International Settlements website using Python.'
---

## Context

To my knowledge, there is no single place that one can go to get all Federal Reserve speeches at once. The Federal Reserve and the Bank for International Settlements (BIS) provide lists of Federal Reserve speeches and offer downloads of individual speeches, but I did not want to manually download more than a thousand speeches. Instead, I decided I would use web scraping techniques to (1) find a list of all of the possible speeches and their corresponding download URLs and (2) download these speeches in the most responsible way possible. I also observed that the BIS does not display the speeches in plaintext or html, which forced me to download the PDF versions of the speeches. This will make my next steps more difficult since data extraction from PDFs is a less straightforward than from a text file, for example.

To see my python code for this process, see the following file: [speech_scrape.ipynb](https://github.com/bendurham441/fed-nlp/blob/main/data%20collection/speech_scrape.ipynb).

## Getting a list of speeches

As a first, step, I located the page containing a paginated list of central bank speeches for a variety of speeches, namely [Central Bankers' Speeches](https://www.bis.org/cbspeeches/). This page is structured such that retrieving a list of speeches (for a given page) would appear straightforward. Upon first inspection, it would appear that one could search for `<td>` elements and then retrieve the links in the associated `<a>` tags. 

The above outlines my first approach. I first used the `requests` library to retrieve the HTML content of the Central Bankers' Speeches page, and then used `beautifulsoup4` to find the first `<a>` in each `<td>`, but quickly discovered that this approach would not work.

Upon closer inspection, this page dynamically loads the list of speeches after the page loaded using an Asynchonous Javascript and XML (AJAX) request, so the table of speeches doesn't show up when I fetch the page content using the `requests` library. This prompted me to explore the requests made by this page after the initial page load, using Firefox's developer tools. I noticed that the page makes a `POST` request to the API endpoint cbspeeches.htm endpoint. Using the browser extension [Copy as Python Requests](https://addons.mozilla.org/en-US/firefox/addon/copy-as-python-requests/), I was able to extract the data required by this endpoint.

I then modified the code to vary the `page` parameter, so I could create a function to a list of the speeches listed on any given page, which I used to cycle through all of the possible pages, gathering a list of all of the Federal Reserve speeches available on the BIS website. To avoid getting ip-blocked and also to be courteous to the BIS, I added a random delay of at least 10 seconds. While this did elongate the process of data gathering, I did not want to overwhelm the BIS webiste by making many requests in quick succession. In the end, I ended up with a list of the relative paths of all of the speech PDFs.

## Retrieving the speeches

With this list of the relative paths of the speeches, I then created another function to download a speech given a relative path on the BIS webiste. I then used a similar delay procedure to download all of the speeches I identified in the last step and store them on my computer. 

## Next Steps

Now that I have all of the source PDFs, I will detail the process of extracting the text from the PDFs in my next article.