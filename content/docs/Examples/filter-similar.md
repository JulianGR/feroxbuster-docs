---
title: "Filter by Page Similarity"
weight: 12
categories: ["examples"]
tags: ["feature", "filtering", "similar", "fuzzy"]
description: >
  Added in version **1.11.0** updated in **2.8.0**
---

## Filter Response by Similarity to A Given Page

{{% alert title="Heads up" color="warning" %}}
- Using a bunch of `--filter-similar-to` values **may** negatively impact performance
{{% /alert %}}

Version 1.11.0 adds the ability to specify an example page for filtering pages that are similar to the given example.

Version 2.8.0 updated the underlying algorithm from SSDeep to Simhash.

For example, consider a site that attempts to redirect new users to a `/register` endpoint. The `/register` page has a
CSRF token that alters the page's response slightly with each new request (sometimes affecting overall length). This
means that a simple line/word/char filter won't be able to filter all responses. In order to filter those redirects out,
one could use a command like this:

```
./feroxbuster -u https://somesite.xyz --filter-similar-to https://somesite.xyz/register
```

`--filter-similar-to` requests the page passed to it via CLI (`https://somesite.xyz/register`), after which it hashes 
the response body using the [Simhash algorithm](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/33026.pdf).  All subsequent 
pages are hashed and compared to the original request's hash. If the comparison of the two hashes meets a certain 
percentage of similarity (currently a hamming distance <= 3) then that request will be filtered out.

Simhash was selected as it does a good job of identifying near-duplicate pages while remaining performant.  Other algorithms were tested but resulted in huge performance hits (orders of 
magnitude slower on requests/second).
