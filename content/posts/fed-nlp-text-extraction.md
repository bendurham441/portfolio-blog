---
title: "Extracting Text from Fed Speech PDFs using PyPDF2"
date: 2023-09-13T15:46:26-04:00
tags: []
draft: false
description: 'In my last post, I described the process to retrieve PDFs of Federal Reserve speeches from the Bank for International Settlements website. This post describes my process for extracting plain text from the PDFs so I can work with it more easily.'
---

## Introduction

As I noted in my last post, the Bank for International Settlements (BIS) maintains an archive of central bank speeches for many countries, but only posts snippets of these speeches on their website. The full speeches can only be accessed as PDFs. This adds another step into the data collection process. If the speeches were already in plain text on the BIS website, I could more easily just scrape the text and store it locally, since extracting text from a structured webpage is usually straightforward, or at least more straightforward than extracting data from a PDF. But since the full speeches are only available in PDF form, I needed to create a way of extracting the text from the PDFs. The code related to this post can be found in [this file](https://github.com/bendurham441/fed-nlp/blob/main/data%20collection/pdf_extract.ipynb).

## The Goal

My goal in this process was to extract the title, author, date, and the text content of the speeches from the PDFs so I can store it in a csv file for use in various NLP tasks.

## Identifying A Starting Point

Thankfully, these speeches from the BIS website are mostly well formed. By this, I mean that they contain actual text, as opposed to being scans (effectively images) of speeches. This is very important, because if the speeches were scans or images of speeches, extracting text would require the use of Optical Character Recognition (OCR) techniques, instead of Reguar Expressions (RegEx).

When using RegEx to extract text from documents, the difficulty of extracting text from a group of PDFs varies based on the consistency in format across the PDFs. If all of the PDFs are structured in the same way, one need only take a model document, figure out RegEx patterns that can extract the text from this document, and then apply these regular expressions to all of the PDFs in the corpus. But if document formats vary significantly, one needs to identify representatives of each of the formats, determine characteristics that can be used to distinguish different formats, and create text extraction processes for each format. Thankfully, the Federal Reserve speeches on the BIS website have only a few different formats, so I only needed to create two format-specific information extraction processes.

I started by visually inspecting a few speeches, making sure to include speeches from different periods of time in case the format changed after a certain date. I discovered that most speeches match the format represented by the following example: [Welcoming remarks - "Fed Listens"](https://www.bis.org/review/r230808a.pdf). In fact, in my relatively cursory exploration of speeches, I did not encounter any other formats, convincing me that all or at least most of the speeches match this format. For this reason, I proceeded by creating a text extraction procedure for this format, with the intent of applying this procedure to all of the documents in the corpus and then identifying other formats by looking for cases where this procedure failed.

## Text Extraction from the Most Common Format

In this section, I describe the procedure I used to extract the relevant data from the most common format, represented by [this speech](https://www.bis.org/review/r230808a.pdf). The following section depends on using PyPDF2's PdfReader object, which can be instantiated as follows:

```
from PyPDF2 import PdfReader

reader = PdfReader(os.path.join(folder_name, file_name))
```

### Using PDF Metadata

When possible, I used PyPDF2 to extract the title, author, and date from PDF metadata using the `reader.metadata` object. For example, I noticed that the `/Title` metadata attribute of this most common format fits the form "[author]: [title]." Therefore, I could easily extract these two pieces of information by using `split(':')` to separate the author from the title. Additionally, I noticed that the `/Subject` metadata field contained a brief description of the speech but also the date of the speech, expressed in one of two formats:

1. 2 January, 2009
2. January 2, 2009

A select few omit the comma, so I also account for that. Under the assumption that `/Subject` of the speech is unlikely to contain more than one full date, I use RegEx to extract strings matching one of these date formats:
```
[0-3]?[0-9]{1} [A-Z][a-z]{2,8},? [0-9]{3,4}|\w+ [0-3]?[0-9],? [0-9]{4}
```
For format 1, I use the first pattern which appears before the `|`. I will walk through this example step by step. The first part, `[0-3]` captures any number between 0 and 3. The next part, "`?`", tells RegEx to capture strings with 0 or 1 occurence of the preceeding characters. This makes more sense when combined with the next section, which captures exactly one occurence of an integer between 0 and 9. Together, these capture any number between 0 and 39, also accounting for leading zeroes, such as a case like "02 January, 2009."

The next part captures a capitalized month, by looking for strings that start with a capital letter followed by 2 to 8 lowercase letters, since all months contain between 3 and 9 letters. I also account for the possibility that the month is followed by an optional comma. The final part captures a number with three or four places. I include the possibility that this could be 3 because of a typo I discovered later on where the year was missing a number.

The second format follows a similar procedure, with the order swapped for the month and day.

### Extracting the Content of the Speech

Having taken all I could from the PDF metadata, I moved on to the PDF contents. Extracting text from a PDF using PyPDF2 is as simple as using `extract_text()` on each of the pages in `reader.pages`.

In the case of this particular document format, I noticed that the first page of the speeches contains a preamble, separated from the document by "\* \* \*." For my purposes, I do not need this segment of the document, so I take everything after the preamble by `split`ting on "\*" and taking the last element of the resulting list: `text = text.split('*')[-1]`.

I also observe that each of the pages of the speeches follows this format: "[speech content]\n [footer text]," where the footer contains some such as "BIS - Central bankers' speeches" and a page number. I remove this by selecting slicing the string up to the last occurence of a newline character, which I do as follows: `text = text[:text.rfind('\n')]`. I also opt to exlude footnotes, and use RegEx patterns to remove these from the document. After this, I remove the remaining newline characters, leaving just the text content of the speech.

## Filling in the gaps

The approach above works on the vast majority of documents, but not all of them. To find other formats, I apply this procedure on all of the documents and look for failure points. I detect failures both through finding documents which error-out when this procedure is applied, or if any of the desired fields were empty (date, author, title, text).

After I identify documents that don't fit this form, I'm faced with a choice: to generalize my procedure or to manually input the data. For my purpose, I generalized the procedure to account for the second most common format as well, since there were several examples fitting this alternate form, but for the few (less than 10) remaining documents that did not fit either of these forms, I extracted the data manually by copying the data from the PDFs.

## Conclusion and Next Steps

Now, I have my primary data source in a more digestible format that I can start working with. Next, I will start some exploratory data analysis on these documents before applying topic modeling and sentiment analysis techniques.