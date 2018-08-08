---
title: "Country Stock Market Performance Choropleths with Leaflet for R"
date: 2018-08-08
tags: [Visualization, Choropleths, Finance, R, leaflet, tidyquant]
excerpt: "Choropleths allow the display of statistical variables on shaded regions of maps. For example the GDP of countries or states can be displayed with different color levels. We usually display data according to the geographical administrative subdivision; country, region, state, county, etc. as these are common definitions shared by everyone and statistics are usually available. A judicious choice of color palette will allow us to present the data coherently. For example, GDP data where growth can be positive or negative - a contrasting scale like Red-Yellow-Green will allow better representation compared to a blue only scale. "
comments: true
htmlwidgets: true
---
### Introduction
[Choropleths](https://en.wikipedia.org/wiki/Choropleth_map) allow the display of statistical variables on shaded regions of maps. For example the GDP of countries or states can be displayed with different color levels. We usually display data according to the geographical administrative subdivision; country, region, state, county, etc. as these are common definitions shared by everyone and statistics are usually available. A judicious choice of color palette will allow us to present the data coherently. For example, GDP data where growth can be positive or negative - a contrasting scale like Red-Yellow-Green will allow better representation compared to a blue only scale.
{: style="text-align: justify;"}

### Data
For our choropleth we will display the country ETF performance from based on the list established by [Seeking Alpha])(https://seekingalpha.com/etfs-and-funds/etf-tables/countries). Note that these are country ETFs that represent the performance of a basket of stocks from a certain country and do not necessarily match the performance of the country's main stock index which may track a different basket of stocks. With the list of tickers we can download the price information using the `tidyquant` package. We will also need the spatial polygons of various countries in order to create our choropleth. This is available online, for example from [Natural Earth Data](http//www.naturalearthdata.com). This [link]((http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/50m/cultural/ne_50m_admin_0_countries.zip)) will start the download on your browser. The spatial data is also available on the post's [github repository](({{site.url }}{{site.baseurl }}/data/ne_50m_admin_0_countries)).The unzipped folder should be placed in the working directory. The `rgdal` library is required to read the file.
{: style="text-align: justify;"}

```r
library(tidyverse)
library(rgdal)
etf<-read.csv('country_etf.csv')
world <-readOGR("./ne_50m_admin_0_countries",
'ne_50m_admin_0_countries', verbose = FALSE)
```
Using `tidyquant` we can download the ETF price data from providers like Yahoo using the function `tq_get`. `tidyquant` is compatible with `tidyverse` as all functions return `tibbles`and we can use commands like `group_by`. `tq_transmute` with the function `periodReturn` set to `monthly` produces a new `tibble` which is resized to the new period.
{: style="text-align: justify;"}


```r
library(tidyquant)
Price<-etf$Ticker %>%
  tq_get(get="stock.prices",
         from="2018-01-01",
         to="2018-07-31") %>%
  group_by(symbol)

gain_m<-Price%>%
  tq_transmute(selected=adjusted,
                  mutate_fun=periodReturn,
                  period="monthly",
                  col_rename="R_monthly"
                 ) %>%
  filter(row_number()==n())

gain_m$R_monthly<-gain_m$R_monthly*100
```
We then merge the datasets. Our returns need to be merge with the shape polygon in order to display the stock returns by country:
{: style="text-align: justify;"}

```r
gain_m<-merge(etf,gain_m,by.x='Ticker',by.y='symbol')
world_etf <- merge(world, gain_m, by.x="NAME",by.y ="Country")
```
### Choropleth
The choropleth is able to display information upon user click. We create `ytdPopup` to display the country name and monthly performance. `colorNumeric` with palette `RdYlGn` is used to create a color scheme for the different stock returns.
{: style="text-align: justify;"}

```r
# Create a popup that displays monthly performance.
library(leaflet)
ytdPopup <- paste0("<strong>Country: </strong>",
                world_etf$NAME,
                "<br><strong> Monthly performance: </strong>",
                round(world_etf$R_monthly,1), "%")

ytdPal <- colorNumeric("RdYlGn", na.omit(world_etf$R_monthly), n=20)
```

```r
leaf_world_etf <- leaflet(world_etf) %>%
  addTiles(options=tileOptions(opacity=0.6)) %>%
  #addProviderTiles("CartoDB.Positron") %>%
  setView(lng =  20, lat =  15, zoom = 2) %>%
      addPolygons(fillOpacity = 0.6,
                  fillColor =~ytdPal(R_monthly),
                  color = "white",
                  weight = 1,
                  opacity=1,
                  dashArray = "",
                  layerId = ~Ticker, popup = ytdPopup) %>%
    addLegend("bottomright", pal=ytdPal,
              values=~na.omit(R_monthly),
    title = "Monthly",
    opacity = 0.6
  )

leaf_world_etf
```

<div id="htmlwidget-9fe26909812a93ad4173" style="width:600px;height:400px;" class="leaflet html-widget"></div>
{% include stock_map.html %}
