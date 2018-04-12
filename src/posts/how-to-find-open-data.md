---
title: "How to find open data"
date: "2018-04-09"
tags: ["open data", "how to"]
---

We talk a lot about "open data" around here. We define open data as information about city services and operations available in the public domain, meaning for free without copyright.

Sometimes we interchange "open data" with "open data portal", which refers to the website where we publish open data in machine-readable formats. 

If you aren't familiar with these terms yet, this post is a primer on navigating the types and formats of information that the City of Detroit offers on our open data portal. To follow along, go to [data.detroitmi.gov](https://data.detroitmi.gov/).

### What's a data portal?

An open data portal is a website where you can search for public records and download data files. Datasets can be formatted as tables, maps, and visualizations. Anyone can access our data and use it in their own projects by downloading it for Excel or making an [API request](https://dev.socrata.com/?ref=Detroit).

In Detroit's portal, you'll find data points that are quantitative, like the number of [building permits](https://data.detroitmi.gov/d/xw2a-a7tf) issued last week, and more qualitative in nature, like an [organization flow chart of the Mayor's office](https://data.detroitmi.gov/d/bkt5-fjcc). Many of our data are organized by address or parcel number.

If you've ever looked for open data in other cities, you may have noticed similar web interfaces. Detroit's open data portal is built on a platform called [Socrata](https://socrata.com/), the same one used in [Chicago](https://data.cityofchicago.org/), [NYC](https://opendata.cityofnewyork.us/), [San Francisco](https://datasf.org/opendata/), and many other cities.

### Where do I start? 

From the homepage of [data.detroitmi.gov](https://data.detroitmi.gov/), you'll find navigation links in the blue header bar followed by a grid of data topics and key tools. Here's an overview of what those do:

- Links:
  - [About](https://data.detroitmi.gov/about): Read about the Executive Order that established Detroit's open data program
  - [Browse Datasets](https://data.detroitmi.gov/browse): See all of our open data, sorted by "Most accessed" by default
  - [Nominate](https://data.detroitmi.gov/nominate): Suggest a new dataset and see past nominations
  - [Developer Docs](http://dev.socrata.com/?ref=Detroit): Read technical instructions for accessing data through the API
  - [Changelogs](https://cityofdetroit.github.io/iet/tags/open-data-changelog): See what's new month by month

![image of data.detroitmi.gov homepage](https://github.com/iet/src/img/socrata-grid.png "data.detroitmi.gov")

- Grid: The eight blue boxes represent categories used to organize datasets within the portal. Scroll over each to see an explanation; click to see a list of datasets in that category.

- Tools: The four images in the bottom row of the grid link to additional resources that live *outside* of the open data portal. These include visualization apps built by our team as well as a [Feedback Form](https://app.smartsheet.com/b/form?EQBCT=2cfb2a637f0f49e197ef78e397e76eb9) where you can ask open data questions.

### What's in a dataset?

From the "Browse Datasets", select an item that is called type "[Dataset](https://data.detroitmi.gov/browse?limitTo=datasets)" (look for the block icon in the upper right). Try [Detroit Demolitions](https://data.detroitmi.gov/d/rv44-e9di) as an example.

All datasets on the portal have two key parts: metadata and a table view. Metadata is high-level information that describes the data. The table view is an Excel-like view of columns and rows formatted for the web.

Let's start with metadata. It includes a description of the dataset, the date it was last updated, the update frequency, associated city agencies or departments, the number of rows, the names and data types of all columns, and a table preview. Below the table preview, you can find "Related Content Using this Data". Related content may include maps or a "Data Lens" page, which is a collage of charts, graphs, filter functions and smaller maps specific to the Socrata platform.

To switch to the table view, click the blue "View Data" button at the top of the metadata page. Use the menu at the top of any column to sort values in ascending or descending order or filter a single value. If you're looking for a specific value like a name or address within the data, use the text search box in the upper right with the placeholder, "Find in this Dataset".

The colored buttons along the top right of the table offer more options, notably click "Export" to see all available formats for download.

### Stay tuned

We plan to follow up this post with future how-to style open data tutorials, like how to create your own custom visualizations within the open data portal, how to use the API, and more. If you have suggestions or ideas, send them our way at [iet@detroitmi.gov](mailto:iet@detroitmi.gov).