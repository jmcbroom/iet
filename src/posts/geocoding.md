---
title: "Address & geocoding primer"
date: "2018-06-06"
tags: ["geocoding", "addresses"]
---

Let's talk about addresses, why they're important, and how we can use them to derive more information about a place.

Addresses are probably the most common method used to refer to specific places, like `2 Woodward Avenue`. You may also come across:

- intersections: `6 Mile and Wildemere`,
- ranges: `Cadieux between Warren and Cincinnati`
- coordinates: `(42.842354, -83.102335)`

However - the *vast* majority of location data we process at the city comes in the form of an address. The city sends mail to addresses, assigns addresses to new structures being built, and so much more. Because addresses are so omnipresent, tools that can properly understand address input and return useful data about that address are essential infrastructure that underly nearly every tool we build.

As an example, a very common operation in our open data processes is __geocoding__: the process which turns a text address (`2 Woodward Avenue`) into coordinates which can be plotted on a webmap (`42.329543, -83.043720`), or a parcel identifier that can be used to join to other datasets (`01004068-86`). In this post, we'll explain how you can utilize the City of Detroit's geocoder in a couple of ways.

## Our three geocoders, and how they work

There's two different geocoders that we have available at the city - understanding their differences is key to getting the specific data that you need.

### Address point

The first one is an __address point geocoder__. It's looking to match an address input against a table of discrete address points (this is called the geocoder's __reference dataset__). The geocoder works like this:

- Accepts an address input: `2 Woodward Avenue`
- Parses that input into address components:
  - the address *house number* is `2`
  - the address *street name* is `Woodward`
  - the address *street type* is `Avenue`
- Finds the best match from the address point table and returns that match

The advantage of the address point geocoder is that the individual address points in the reference dataset can be related to a parcel; therefore, you can ask the geocoder about an address and get the parcel number back, which is a very common use case. This parcel number appears in the `User_fld` field. Here's an example for [326 Hendrie](https://gis.detroitmi.gov/arcgis/rest/services/DoIT/AddressPointGeocoder/GeocodeServer/findAddressCandidates?Street=&ZIP=&Single+Line+Input=326+Hendrie&category=&outFields=*&maxLocations=&outSR=4326&searchExtent=&location=&distance=&magicKey=&f=html).

The disadvantage of this geocoder is that you need to have good input to return a match - you can get away with small typos in the street name, but not much more than that.

### Street centerline

The second option is the __street centerline geocoder__. The reference dataset for this geocoder is a layer of city streets which have an address range attached. The same parsing step is performed, but now the geocoder returns a match based on where your input address *should* exist based on known address ranges.

The main advantage of the street centerline geocoder is that you can match addresses which __don't exist in the address point table__ used by the address point geocoder. The main drawback is that the coordinates returned will be inexact and there will not be a parcel number attached.

Another useful feature of the street centerline geocoder is that it can provide coordinates for intersections; here's an example response for [7 Mile and Asbury Park St.](http://gis.detroitmi.gov/arcgis/rest/services/DoIT/StreetCenterlineGeocoder/GeocodeServer/findAddressCandidates?Street=&City=&ZIP=&Single+Line+Input=7+Mile+and+Asbury+Park&category=&outFields=*&maxLocations=&outSR=4326&searchExtent=&location=&distance=&magicKey=&f=html)

### Composite geocoder

Thankfully, you don't have to choose if you don't want to! There's a __composite geocoder__ which will:

- Accept input
- Parse input
- Check for a match against the __address point__ geocoder
- If there's no address point match, return a match against the __street centerline__ geocoder.

The geocoder will indicate in the response which geocoder was used to derive the match. Here's [the result](https://gis.detroitmi.gov/arcgis/rest/services/DoIT/CompositeGeocoder/GeocodeServer/findAddressCandidates?Street=&City=&ZIP=&SingleLine=4193+trumbull&category=&outFields=*&maxLocations=&outSR=4326&searchExtent=&location=&distance=&magicKey=&f=html) for `4193 Trumbull`, which isn't an address point - you can see the `Addr_type` field tells us that this is a `StreetAddress` and not an `AddressPoint`.

## How to use these in your workflow

### Web interface

The simplest way to access these geocoders is through the endpoint's web interface:

- [Composite](https://gis.detroitmi.gov/arcgis/rest/services/DoIT/AddressPointGeocoder/GeocodeServer/findAddressCandidates)
- [Address point](https://gis.detroitmi.gov/arcgis/rest/services/DoIT/AddressPointGeocoder/GeocodeServer/findAddressCandidates)
- [Street centerline](https://gis.detroitmi.gov/arcgis/rest/services/DoIT/AddressPointGeocoder/GeocodeServer/findAddressCandidates)

This interface is particularly useful for looking up one address at a time.

You'll want to know about three key parameters:

#### Single Line Input (URL parameter: `SingleLineInput`)
This is where your address input should go. In order to work, this should just be a number, street name, and (optional) street type. Don't include "Detroit, MI" or a zip code.

For intersections, this should be formatted as "Street 1 and Street 2" or "Street 1 & Street 2".

#### Output Spatial Reference (URL parameter: `outSR`)
This defines what spatial reference the output coordinates should be in. The default is `2898` - we use this because sometimes we want to specify a distance in feet and not degrees.

However, for the vast majority of uses, you'll want this to be `4326`; that way, you'll get lat-long coordinates that other web tools will understand.

#### Out fields (URL parameter: `outFields`)
You generally want to set this to `*` to return all match fields. Otherwise, you'll just get back coordinates and a cleaned address.

### Batch geocoding

But what if you have a list of addresses and you want to geocode all of them at once?

You can find plenty of tutorials and plugins to enable batch geocoding with your software of choice: [ArcMap](http://desktop.arcgis.com/en/arcmap/10.3/guide-books/geocoding/geocoding-a-table-of-addresses-in-arcmap.htm), [QGIS](https://www.gislounge.com/how-to-geocode-addresses-using-qgis/), and even [Microsoft Office](https://doc.arcgis.com/en/maps-for-office/install-and-configure/configure-a-custom-geocoder.htm).

Alternatively, we've been prototyping a simple geocoding web app that accepts a list of Detroit addresses and returns additional location fields. This tool favors spreadsheet workflows, whether that's a Smartsheet, Google Sheet or CSV. You can copy and paste a list of addresses directly into the app's search and we'll generate a table of results that can be smoothly copy-and-pasted right back into your original workspace.

Give it a spin here: https://cityofdetroit.github.io/geocode/

## Developer workflows, and where you'll find geocoding in our code

As we mentioned earlier, nearly all of the apps that our team builds rely on being able to request address information from our geocoder. Here's two generalized examples of how we commonly implement geocoding in Javascript and Python.

### Javascript

Let's say we're tasked with building a web map. This web map will have a search box that accepts an address as text input. When someone searches an address, the map should fly-to that place. So, we'll need to read this input and request the address' associated lat-lng coordinates from the geocoder to reposition the center of our map.

Assuming you're working in an environment that supports the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API), you might write a function like this:

```js
/**
 * Returns more information about an address from the AddressPointGeocoder
 * @param {string} address
 * @returns {Promise} Promise object represents a list of matches 
 *   each match has lat-lng coordinates, a parcel identifier, and more
 */
function geocodeAddress(address) { 
  let matches = [];
  let url = `https://gis.detroitmi.gov/arcgis/rest/services/DoIT/AddressPointGeocoder/GeocodeServer/findAddressCandidates?  Single+Line+Input=${address}&outSR=4326&outFields=*&f=pjson`;
  
  return fetch(url)
    .then(response => response.json())
    .then(data => {
      matches.push(data.candidates[0]);
      return matches;
    })
    .catch(error => console.log(error));
}
```

This function will return nice JSON `matches` that we can parse accordingly in our app. The geocoder orders candidates by `"score"`; we're choosing to just keep the first and most likely candidate above.

Try it out live in this [Observable Notebook](https://beta.observablehq.com/@jessicamcinchak/geocoding-blog-example).

### Python

When it comes to geocoding in Python, we'll point you straight to the [ArcGIS API for Python](https://developers.arcgis.com/python/). You'll find many useful methods for working with geospatial data in this library, like:
- [geocoding](https://developers.arcgis.com/python/guide/understanding-the-geocode-function/) a single location
- [batch geocoding](https://developers.arcgis.com/python/guide/batch-geocoding/) up to 1,000 addresses at a time
- [reverse geocoding](https://developers.arcgis.com/python/guide/reverse-geocoding/) a x-y coordinate to return a street address

We use the `arcgis` library in our ETL workflows for open data. We commonly have a table of data where one column is an address and we want to add additional columns with location coordinates and a parcel number. Check out [this Python class](https://github.com/CityOfDetroit/etl/blob/master/etl/geocode.py) to see how it works.
