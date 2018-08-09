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
For our choropleth we will display the country ETF performance from based on the list established by [Seeking Alpha](https://seekingalpha.com/etfs-and-funds/etf-tables/countries). Note that these are country ETFs that represent the performance of a basket of stocks from a certain country and do not necessarily match the performance of the country's main stock index which may track a different basket of stocks. With the list of tickers we can download the price information using the [tidyquant](https://github.com/business-science/tidyquant) package. We will also need the spatial polygons of various countries in order to create our choropleth. This is available online, for example from [Natural Earth Data](http//www.naturalearthdata.com). This [link]((http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/50m/cultural/ne_50m_admin_0_countries.zip)) will start the download on your browser. The spatial data is also available on the post's [github repository](({{site.url }}{{site.baseurl }}/data/ne_50m_admin_0_countries)).The unzipped folder should be placed in the working directory. The `rgdal` library is required to read the file.
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
We then merge the datasets. Our returns need to be merged with the shape polygon in order to display the stock returns by country:
{: style="text-align: justify;"}

```r
gain_m<-merge(etf,gain_m,by.x='Ticker',by.y='symbol')
world_etf <- merge(world, gain_m, by.x="NAME",by.y ="Country")
```
### Choropleth
The [R leaflet package](https://rstudio.github.io/leaflet/) allows us to control the popular [leaflet](https://leafletjs.com/) JavaScript library without writing any code in Java. The package allows us to plot many geospatial visualizations, including choropleths. The choropleth can be set to display information when clicked. We create `Popup` to display the country name and monthly performance.
{: style="text-align: justify;"}

```r
# Create a popup that displays monthly performance.

Popup <- paste0("<strong>Country: </strong>",
                world_etf$NAME,
                "<br><strong> Monthly performance: </strong>",
                round(world_etf$R_monthly,1), "%")
```
`colorNumeric` with palette `RdYlGn` is used to create a color scheme for the different stock returns. We want the palette to be symmetric about zero. In order to achieve this, we need to **provide an interval that is centered around zero** to the parameter `domain` of colorNumeric. We achieve this by extending the original domain of the data that we want to plot, depending if the negative or positive component is larger.
{: style="text-align: justify;"}

```r
library(leaflet)
dom= na.omit(world_etf$R_monthly)
a=max(abs(min(dom)),max(dom))
monPal <- colorNumeric("RdYlGn", domain =c(-a,a), n=20)
```
We can then use the `leaflet` function to map our data. The `addTiles` function calls the tile from OpenStreetMap by default. The function `addPolygons` is where we specify how the country polygons should be added. `fillColor` is set to map monthly returns based on `monPal`.

```r
leaf_world_etf <- leaflet(world_etf) %>%
  addTiles(options=tileOptions(opacity=0.6)) %>%
  setView(lng =  20, lat =  15, zoom = 2) %>%
      addPolygons(fillOpacity = 0.6,
                  fillColor =~monPal(R_monthly),
                  color = "white",
                  weight = 1,
                  opacity=1,
                  dashArray = "",
                  layerId = ~Ticker, popup = Popup) %>%
    addLegend("bottomright", pal=ytdPal,
              values=~na.omit(R_monthly),
    title = "Monthly Performance %",
    opacity = 0.6
  )

leaf_world_etf
```
Our choropleth is now ready. Notice that the color legend has yellow for zero. If we did not provide an interval centered around zero to `domain` for `colorNumeric`, there would have been an offset which can confuse users. Clicking on the various countries will display the country name and monthly performance in a pop-up. The user can zoom in/out and also pan the display for more information.
{: style="text-align: justify;"}

<div id="htmlwidget-8117a5c62e2a5a621f7e" style="width:750px;height:450px;" class="leaflet html-widget"></div>
{% include stock_map.html %}

Note that the html-widget does not pull fresh the data so our choropleth will reflect the stock performance for the month of July 2018. A table would probably be more efficient to display stock monthly gains, but in our case we are just using the data to illustrate how choropleths work.
{: style="text-align: justify;"}
