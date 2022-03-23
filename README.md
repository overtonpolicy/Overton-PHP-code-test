# Overton code test

## Introduction

*Overton's users are typically groups who want to influence government policy - they might be universities, charities like the British Heart Foundation or agencies like UNICEF. To help them we maintain a large, regularly updated database of government documents and use text mining, pattern matching and other techniques to extract a list of the people, academic papers and ideas that went into creating each one. We can then give each user a list of areas where we can see they've had an effect.*

Thanks for agreeing to run through this exercise.

During it you'll be tackling a short challenge based on tasks & themes from the real life Overton codebase. You'll be collecting data, parsing it and then feeding it through a simple data processing pipeline. We're keen to see what you come up with!

You should be able to finish the exercise in about an hour. After you've submitted the test we'll schedule a call where you'll have the opportunity to walk us through it.

Please don't spend too much longer than 90 minutes on this: it's fine to leave parts out. The aim _isn't_ for you to produce perfect, production ready code; rather it's for you to be able to get a feel for what Overton does and for you to show us how you approach new projects and problems. We're interested in your thinking as much as the final output and this test gives us lots of jumping off points for chatting later, when you walk us through your work.

We'd like you to code in PHP, but you can use any (or no) framework or helper libraries you like.

## 1. Download data from gov.uk

We're going to collect some links from gov.uk, where the UK government stores policy papers and other documents.

Write some code to download these three web pages:

```
https://www.gov.uk/search/policy-papers-and-consultations?content_store_document_type%5B%5D=policy_papers&order=updated-newest
https://www.gov.uk/search/policy-papers-and-consultations?content_store_document_type%5B%5D=policy_papers&order=updated-newest&page=2
https://www.gov.uk/search/policy-papers-and-consultations?content_store_document_type%5B%5D=policy_papers&order=updated-newest&page=3
```

And then extract the search results you see (just do the ones on this page for now, don't paginate further).

The links you extract may be absolute (e.g. https://www...) or relative (e.g. /documents/xyz ). Normalize them to all be absolute and trim any excess whitespace. You can safely assume that all the relative URLs are based on https://www.gov.uk/

Tips:

* We'd definitely recommend implementing some very simple caching to speed up multiple runs while testing
* At Overton we use the DOM extension, specifically DOMDocument and DOMXPath classes
* An xpath you can use to find the relevant links on this webpage is: 

```
//div[contains(@class, 'finder-results')]//li[contains(@class, 'gem-c-document-list__item')]/a (in the "href" attribute)
```

* It's likely that in the future we'll want to avoid calling the same website more than once a second
* It's likely that in the future we'll want to specify a user agent string in the web request

For discussion later:

* Imagine we have a list of 20,000 pages to download from approx 200 different domains - how would you organize things to maximize throughpout while remaining a good internet citizen (don't call the same domain more than once every, say, 5 seconds)?
 
## 2. Process each link

Using the list of links you generated in step 1 - take the first 50 and discard the rest.

Write some code to download the web pages pointed to by the 50 links and then parse each one, to extract:

* The title of the page: the xpath for this is:

```
//meta[@property='og:description'] (in the "content" attribute)
```

* The authors of the page: note there may be more than one. The xpath for this is: 
  
```
//div[contains(@class, 'gem-c-metadata')]//a[contains(@class, 'govuk-link')] (in the text of the DOM node)
```

Write some tests for the code you use to parse each webpage. There is some example HTML (downloaded from gov.uk) in the fixtures/ directory: one test file has a single author and a simple title, one has two authors and another has multiple authors and an accented é in the title.

Tips:

* We may want to extend your code to extract other bits of metadata from each web page later on

For discussion later:

* What other approaches could we take to extracting titles, authors and publication dates from pages like these?

## 3. Analyzing the data

We want to produce a report showing which organizations are producing content published on gov.uk.

Using the authors data you gathered in step 2, write code to create a simple HTML file that contains an ordered list of gov.uk authors showing how many documents in our set of 50 they are associated with (the department with the most number of documents should be at the top of the list).

The resulting web page doesn't need to look beautiful!

Save this HTML to disk.


## 4. COMPLETELY OPTIONAL - dealing with domains

In the real Overton pipeline we download PDF files and convert these to text and layout information. We'll skip that step here: instead let's use the data/example_document_fulltext.html file.

Write some code that loads this file and extracts all links matching this xpath expression:

```
//li[@role='doc-endnote']//a[@rel='external']
```

Extract the domain of each link and keep track of how often it is seen, then create a simple HTML file that contains an ordered list showing each domain and how often it has been cited.

For discussion later:

* In the future we might want to handle spurious subdomains like www, www2 etc. rather than treating www.lse.ac.uk and www2.lse.ac.uk as separate domains. However, some top level sites like gov.uk have more important subdomains that we'd want to keep e.g. for www.ons.gov.uk we'd want the domain to be ons.gov.uk

## 5. Send us the code

Commit your code to github and make it public and send us the URL or invite us as a collaborator (the username to invite is "mrstew")