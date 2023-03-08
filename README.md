# Overton code test (April 2022)

## Introduction

*Overton's users are typically groups who want to influence government policy - they might be universities, charities like the British Heart Foundation or agencies like UNICEF. To help them we maintain a large, regularly updated database of government documents and use text mining, pattern matching and other techniques to extract a list of the people, academic papers and ideas that went into creating each one. We can then give each user a list of areas where we can see they've had an effect.*

Thanks for agreeing to run through this exercise.

During it you'll be tackling a short challenge based on themes from the real life Overton codebase. You'll be collecting data, parsing it and then feeding it through a very simple data processing pipeline. We're keen to see what you come up with!

You should be able to finish the first three parts of the exercise in about an hour. After you've submitted the test we'll schedule a call where you'll have the opportunity to walk us through it.

Please don't spend too much longer than 90 minutes in total on this: it's fine to leave parts out. The aim _isn't_ for you to produce perfect, production ready code; rather it's for you to be able to get a feel for what Overton does and for you to show us how you approach new projects and problems. We're interested in your thinking as much as the final output and this test gives us lots of jumping off points for chatting later, when you walk us through your work.

We'd like you to code in PHP or Python, but you can use any (or no) framework or helper libraries you like. It'd be useful for the end result to include a script that can be run from the CLI - Preferably with some kind of progress outputs.

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
* At Overton we use PHP's DOM extension, specifically DOMDocument and DOMXPath classes
* An xpath you can use to find the relevant links on this webpage is: 

```
//div[contains(@class, 'finder-results')]//li[contains(@class, 'gem-c-document-list__item')]/a (in the "href" attribute)
```

* It's likely that in the future we'll want to avoid calling the same website more than once a second
* It's likely that in the future we'll want to specify a user agent string in the web request
* It's likely that we'd want to do something very similar with other websites in the future

## 2. Processing each link

Using the list of links you generated in step 1 - take the first 50 and discard the rest.

Write some code to download the web pages pointed to by the 50 links and then parse each one, to extract:

* The title of the page: the xpath for this is:

```
//meta[@property='og:title'] (in the "content" attribute)
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

## 3. Scaling up

Imagine that each day we'll have a new batch of 6,000 pages to download from around 600 different domains - can you quickly sketch out a system that can help us maximize throughput & efficiency while still remaining a good internet citizen?

Some constraints & resources:

* We have a pool of 20 proxy servers that can be used to make calls.
* Each domain has an associated minimum delay X. For gov.uk it might be 10s, for whitehouse.gov it might be 30s etc.
* We don't want to call a domain from any of our IPs more than once every X seconds.
* If we get blocked by a site (we get a 429 response code) we should back off for an hour.
* It doesn't matter what order the links are retrieved in.

You don't need to produce a fully fleshed out design document! We're looking for two things:

* How would you (v roughly) architect this system
* What techniques or approaches might you use in the actual code

We'll go through this together on the call so don't worry about covering absolutely everything, these should mainly be notes for yourself. - That being said it would be good if they could be included in your repository so we can get a brief overview of your thinking ahead of time

## 4. Send us the code

Commit your code to github and make it public and send us the URL or zip up the repository and email it to nick@overton.io
