## Background
This dataset came about because I wanted to investigate whether urban areas in general or only important towns formed nexuses of people using armorial bearings. The right to hold a market provides an alternative to population as a proxy for importance .

## Data

The 698 towns entered in the dataset are those from English counties in the county-by-county listings of fairs in *Owen's New Book of Fairs* (1813) (https://books.google.co.nz/books?id=lrdVAAAAcAAJ&pg=PR3&dq=owens+fairs+1813&hl=en&newbks=1&newbks_redir=0&sa=X&ved=2ahUKEwiRiqnjzuz_AhVzilYBHc0JClwQ6AF6BAgJEAI#v=onepage&q=owens%20fairs%201813&f=false) which have one or more weekly market days noted (or in the case of Seeching, Norfolk and Adwalton, Yorkshire a fortnightly one). Data were entered using the MapBox dataset editor (https://www.mapbox.com/) and saved as a `FeatureCollection` in GeoJSON format (http://geojson.org/)

The data for each town is in the following form:

```
{
  "id": "http://data.ordnancesurvey.co.uk/id/4000000074542156",
  "geometry": {
    "coordinates": [
      -0.333735,
      53.740772
    ],
    "type": "Point"
  },
  "properties": {
    "alternate_name": "Kingston upon Hull",
    "county": "Yorkshire",
    "distance_to_london": 173,
    "number_mps": 2,
    "place_name": "Hull",
    "reference": "http://www.british-history.ac.uk/vch/yorks/east/vol1/pp407-412#p3",
    "silver_street": 1
  },
  "type": "Feature"
}
```

The `id` is taken from the July 2023 version of the OS Open Names dataset (https://www.ordnancesurvey.co.uk/linked-data/OpenNames.zip, current version at https://osdatahub.os.uk/downloads/open/OpenNames).

The `coordinates` for the `geometry` are expressed in the order longitude, latitude.

The `county` property uses the spelling as found in Owens, so may have an extra -shire compared to modern usage (e.g. Somersetshire).

The `distance_to_london` is the value in miles given in Owens, and presumably refers to the distance by main road, rather than as the crow flies. For the few cases where Owens did not provide a number, I took the distance given in W.C. Oulton, *Traveller's Guide, or English Itinerary* (1805) (Vol 1: https://www.google.co.nz/books/edition/The_Traveller_s_Guide_Or_English_Itinera/MHUCAAAAMAAJ?hl=en&gbpv=1&printsec=frontcover, Vol 2: https://www.google.co.nz/books/edition/The_Traveller_s_Guide/MnUCAAAAMAAJ?hl=en&gbpv=1). Otherwise the deficiency was fixed by finding the nearest town radially inward towards London for which Owens provided a distance, then adding the modern driving distance as suggested by Google Maps.

The `location_uncertain` property takes a value of `1` when I was not able to determine the historic location of the market.

The `number_mps` property is the number of Members of Parliament as given in Owens. Where present, this has the value 1, 2, or (in the case of London) 4 because Owens was writing before the Great Reform Act of 1832.

The `place_name` is taken from the listing in Owens. The `alternate_name` property is used when the modern name of the town differs from that listed in Owens.

The `reference` property is present when I needed to use additional information to determine the historic location of the market. Typically this was the relevant volume of the *Victoria County History*, a conservation area appraisal, or an extensive urban survey (http://archaeologydataservice.ac.uk/archives/view/EUS/).

The `silver_street` property records whether there is a road named "Silver Street" in proximity to the historic market place. This property takes the values `1` (Silver Street leads into the market place), `0.75` (Silver Street is off a road leading into the market place), or `0.5` (elsewhere in the town). This property was added after a first pass through the data when I noticed that this occurred frequently (approximately 10% of market towns).

For upload to Knowledge Commons I converted the data to CSV format, with the headers `id, latitude, longitude, place_name, county,	alternate_name, distance_to_london, number_mps, silver_street, location_uncertain, reference`

## Methodology

### 1. Locate the town
In most cases this simply required entering the name of the town and county into the search field of the MapBox dataset editor. Where this failed (e.g. because the modern name of the town is different) entering the name and county into Google would frequently return the modern name as a suggested search.

In cases where the historic county had changed as a result of the *1844 Counties (Detached Parts) Act*, it was usually sufficient to check the Wikipedia article for the town in question to confirm the change of county.

In all cases where the modern name of the town differs from that in Owens, this was entered into the `alternate_name` property of the Feature.

### 2. Locate the centre of the town
This usually meant locating the High St. I preferred to use the satellite view for this and the next step as it allowed me to easily eliminate areas of modern development (due to their regular street pattern) and isolate the historic core. For towns which remain small in the present day the historic centre usually coincides with the geographic centre. In slightly larger towns the road names can provide clues, particularly those of compass directions (e.g. North St), roads to neighboring towns (e.g. York Rd), or older words for road (e.g. Hungate) For towns which have expanded significantly over the past two centuries, looking at the late-nineteenth-century Ordnance Survey maps in the side-by-side viewer of the National Library of Scotland (http://maps.nls.uk/geo/explore/side-by-side/) was a good way to locate the historic centre.

### 3. Locate the historic market place
I looked for a large open space adjacent to the main street, or a wide or boat-shaped street. If such a place is named *Market Place*, *Market Square*, or *Market Hill* on either the modern or nineteenth century Ordnance Survey maps this was taken as sufficient evidence of the historic location of the market. Roads named *Market Street* in general were not the site of the market, but led to it. In Devon and Cornwall the road leading to the market place was often called *Fore St*.

If no location was found matching these criteria I consulted other sources of information to find the historic location of the market, in order of preference Extensive Urban Surveys, Conservation Area Appraisals, or Victoria County Histories. The former two were slightly preferable as they usually had a map showing where the market was.

In all cases I was looking for the location of the general market, not livestock markets,

### 4. Mark the location
If there was an obvious focal point, such as a market cross, butter cross, town or market hall, or crossroads in the market place, I placed the marker there, otherwise I placed it in approximately the centre of the market.

Where there were two different locations in the town at which markets were historically held, I chose the larger, or more easily located.

If I was not able to determine the historic location I placed the marker in the centre of the main street, near a crossroads, if any, and added the property `location_uncertain`.
