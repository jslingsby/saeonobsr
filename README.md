
<!-- README.md is generated from README.Rmd. Please edit that file -->

# saeonobsr <img src="saeon_hex.png" align="right" alt="" width="240" />

<!-- badges: start -->

<!-- badges: end -->

saeonobsr provides functions to query and download selected datasets
from the SAEON observations database.

Two functions are made available to users:

`viewDatasets()`

which lists available datasets with the option to limit results to
within a region of interest. The result can be filtered and passed to

`getDatasets()`

which downloads the selected datasets and optionally limits results to
within a specific timeframe

## Installation

You can install the latest version of saeonobsr from
[GitHub](https://github.com/GMoncrieff/saeonobsr) with:

``` r
# install.packages("devtools")
devtools::install_github("GMoncrieff/saeonobsr")
```

## Authorisation

To use saeonobsr you need to first register an account [SAEON
observations database](http://observations.saeon.ac.za/). Once
registered you need to login and retrieve an API token from
<https://observations.saeon.ac.za/account/token>. This token will be
valid for 1 month.

## Quickstart

Before starting set your API access token using

``` r
Sys.setenv(OBSDB_KEY = "xxx")`
```

The code below lists all datasets available for the Cape Peninsula,
South Africa, and downloads daily minimum temperature data for a single
site

``` r
library(saeonobsr)
library(dplyr)
library(sf)

#create spatial bounds
#done using geojson.io
region <- '{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
       "coordinates": [[[
              18.25927734375,
              -34.363843538830665
            ],[
              18.571014404296875,
              -34.363843538830665
            ],[
              18.571014404296875,
              -33.87839688404626
            ],[
              18.25927734375,
              -33.87839688404626
            ],[
              18.25927734375,
              -34.363843538830665
            ]]]}}]
}'
#create sf object
bounds<-st_read(region,quiet = TRUE)

#get a list of available datasets
#then filter to a selected site and observation type
datasets <- viewDatasets(extent=bounds,spatial = FALSE) %>%
      filter(siteName == 'Constantiaberg') %>%
      filter(description == 'Air Temperature - Daily Minimum - Degrees Celsius')

#retrieve the selected datasets for 2019
start = '2019-01-01'
end='2019-12-31'
obs <- getDatasets(datasets,startDate=start,endDate=end)
#> [1] "received dataset with id 137"
head(obs)
#> # A tibble: 6 × 12
#>          id instrumentName         sensorName date  latitude longitude dataValue
#>       <int> <chr>                  <chr>      <chr>    <dbl>     <dbl>     <dbl>
#> 1 103977006 Constantiaberg automa… Constanti… 2019…    -34.1      18.4     11.1 
#> 2 103977008 Constantiaberg automa… Constanti… 2019…    -34.1      18.4      8.93
#> 3 103977010 Constantiaberg automa… Constanti… 2019…    -34.1      18.4      8.77
#> 4 103977012 Constantiaberg automa… Constanti… 2019…    -34.1      18.4     10.5 
#> 5 103977014 Constantiaberg automa… Constanti… 2019…    -34.1      18.4     11.6 
#> 6 103977016 Constantiaberg automa… Constanti… 2019…    -34.1      18.4     10.1 
#> # … with 5 more variables: description <chr>, phenomenonName <chr>,
#> #   obs_type_code <chr>, offeringName <chr>, unitName <chr>
```
