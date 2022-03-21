---
author: "catherinekll"
title: "Introduction to SIEM tool - Splunk"
date: "2022-03-21"
description: "Basic Introduction to Splunk"
tags: ["writeups", "SIEM", "Splunk"]
ShowBreadcrumbs: False
---

## Some background information...

I actually only started learning and using [Splunk](https://www.splunk.com/) for less than a week for my internship role as a Security Operations Center Analyst, so this would be a very general and introductory writeup post for now, but may be subjected to more updates in the future :)

## What is Splunk? and what can it do?

Splunk is a software that works with information from incoming requests, outgoing messages from the various platforms and assets a company/organisation owns. It helps to oragnise, analyse and visualise such machine data into managable reports, alerts, dashboards that can be used as a security and incedent management tools.

## Understanding the Splunk Interface

1. The Search Bar
2. The Events Table
3. The Fields Sidebar

This image shows the search results on the **Events** tab.

{{< figure align=center src="/images/splunk/splunk-events.png" caption="Image Credits - Splunk Interface (https://docs.splunk.com/)" >}}

### The Search Bar

#### Time Setting

> Limiting a search by **time** is key to faster search results and is a best practice.

#### Timeline

It serves as a visual representation of events segmented over time. It supports additional features such as selecting a specific time range and zooming in.

#### Search Mode

There are 3 different types of search modes for differenet levels of field discovery:

1. **Fast Mode**

This mode is used when you know all the fields you want to search and you specify them in the search, the search will run extremely fast and do not use any of the processing power to discover fields (field discovery is disabled in this mode)

2. **Smart Mode (default)**

This mode Splunk attempts to determine what results you are looking for and return them as quickly as possible based on the structure of your search, it attempts to discover fields in the data by looking for key value pairs. If your search contains transforming commands like stats chart or time chart, then smart mode acts more like fast mode.

3. **Verbose Mode**

This mode is used when the user does not know much about the data and need splunk to expend its full resources auto discovering fields. Verbose mode returns all event and field data it can possibly find at the cost of a slower search.


### The Events Table

Displays the time and details of each event.

Event results are displayed in a reverse chronological order, meaning we will see the most recent data first and then the least recent data last.

### The Fields Sidebar

> Fields are essentially searchable Key-Value pairs

This sidebar contains the most important fields that have values in at least 20% of the events.

The default fields that would always appear are `Host`, `Source` and `Sourcetype`.

The 2 type of symbols that appear before the field names are:

- `a` -  denotes string value

- `#` - denotes a numeral



## How to use the Search Bar?

Here I will give a general overview and introduction of some of the best commands and search practices when using Splunk:

{{< figure align=center src="/images/splunk/splunk-search-language.png" caption="Image Credits - Use the search language (https://docs.splunk.com/)" >}}

### Search Terms

The foundations of a search query. It is best to perform filtering at this stage first with some of the larger default fields (eg. `index`,`Host`, `Source` and `Sourcetype`). E.g.:

#### Host field is filtered only at the end (Slower)

```splunk
index=foo | stats count by host |search host="bar"
```

#### Host field is filtered at the start (Faster)

```splunk
index=foo host="bar" | stats count by host
```

- **Command** - (If a command referenes a specific value, that value will be case sensitive) tells splunk what we want to do with search results (eg. creating charts, computing statistics and formatting)
- **Function** - explains how we want to chart, compute and evaluate the results
- **Argument** -(case sensitive) variables that we want to apply to the function
- **Clause** - explain how we want results grouped, or defined

## Summary of Best Practices

1. Search terms, command names, clauses and functions are not case sensitive.
2. Limiting time range is always the best way to search.
3. `index`, `source`, `host` and `sourcetype` are the most powerful.
4. The more you tell the search engine, the more likely it is that you will get good search results (eg. better to search `"failed password"` then to just search for `"password"`).
5. Inclusion is always better than exclusion (eg. searching for “access denied” is better than searching for NOT “access granted”).
6. When possible, use the `OR` or `IN` operators instead of wildcards (`*`).
7. Apply filtering commands as early in your search as possible so as to make future manipulations of the data faster.

*This is really a superrr brief intro lolol, but it kinda helped me get a bit more familiar with Splunk as well, hope yall will find it a bit helpful as well :D*

## References

- [Splunk Best Practices](https://www.splunk.com/en_us/blog/customers/splunk-clara-fication-search-best-practices.html)
- [Splunk Education](https://education.splunk.com)
- Splunk's documentation and community are really good resources as well plus Google of course haha
