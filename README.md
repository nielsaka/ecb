### Introduction

The `ecb` package package provides an `R` interface to the [European Central Bank's Statistical Data Warehouse](https://sdw.ecb.europa.eu/).

To install the development version:

``` r
library(devtools)
install_github("expersso/ecb")
```

### Example usage

The following example extracts the last twelve observations of headline and "core" HICP inflation for a number of countries available in the `ICP` database. See details below on how to use the `filter` parameter and how to find and use the SDW series keys.

``` r
library(ecb)
library(ggplot2)

key <- "ICP.M.DE+FR+ES+IT+NL+U2.N.000000+XEF000.4.ANR"
filter <- list("lastNObservations" = 12, "detail" = "full")

hicp <- get_data(key, filter)

ggplot(hicp, aes(x = obstime, y = obsvalue, color = title)) +
  geom_line() +
  facet_wrap(~ref_area, ncol = 3) +
  scale_y_continuous(breaks = pretty_breaks(5)) +
  scale_x_date(date_labels = "%m-%Y", date_breaks = "3 months") +
  theme_bw(8) +
  theme(legend.position = "bottom") +
  labs(x = NULL, y = "Percent per annum\n", color = NULL,
       title = "HICP - headline and core\n")
```

![](plot_example-1.png)

### Details

#### The filter option

The `filter` option of `get_data()` takes a named list of key-value pairs. If left blank, it returns all data for the current version.

Available filter parameters:

-   `startPeriod` & `endPeriod`
    -   `YYYY` for annual data (e.g.: 2013)
    -   `YYYY-S[1-2]` for semi-annual data (e.g.: 2013-S1)
    -   `YYYY-Q[1-4]` for quarterly data (e.g.: 2013-Q1)
    -   `YYYY-MM` for monthly data (e.g.: 2013-01)
    -   `YYYY-W[01-53]` for weekly data (e.g.: 2013-W01)
    -   `YYYY-MM-DD` for daily data (e.g.: 2013-01-01)
-   `updatedAfter`
    -   A timestamp to retrieve the latest version of changed values in the database since a certain point in time
-   Example: `filter = list(updatedAfter = 2009-05-15T14:15:00+01:00)`
-   `firstNObservations` & `lastNObservations`
    -   Example: `filter = list(firstNObservations = 12)` retrieves the first 12 observations of all specified series
-   `detail`
    -   Possible options: `full/dataonly/serieskeysonly/nodata`
    -   `dataonly` is the default
    -   Use `serieskeysonly` or `nodata` to list series that match a certain query, without returning the actual data
    -   An alternative to using `serieskeys/nodata` is the convenience function `get_dimensions()`, which returns a list of dataframes with dimensions and explanations (see extended example below).
    -   `full` returns both the series values and all metadata. This entails retrieving much more data than with the `dataonly` option.
-   `includeHistory`
    -   `false` (default) returns only version currently in production
    -   `true` returns version currently in production, as well as all previous versions

See the [SDW API](https://sdw-wsrest.ecb.europa.eu/) for more details.

#### Using SDW keys

-   Dimensions
-   Wildcards
-   AND (+) operator
-   Using "details" = "dataonly" or `get_dimension()`

### Extended example

-   Restrict with start/end
-   Look up dimensions
-   Merge datasets

### Disclaimer

This package is in no way officially related to, or endorsed by, the ECB.
