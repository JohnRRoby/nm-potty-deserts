
library(tidyverse)
library(leaflet)
library(maps)
library(htmltools)
library(geojsonio)
library(sp)#this is called later on but i specify it here because later on we'll need the `merge` function from this package, not the base one.
library(htmlwidgets) #for export as interactive widget
library(mapview)#used to export dataviz as a static image, completely optional

#read in the data, merge the tables and calculate percentage of housing units that lack complete plumbing.
housing17 <- read.csv("housing17.csv", stringsAsFactors = F)
plumbing17 <- read.csv("plumbing17.csv", stringsAsFactors = F)
pottyViz <- merge(housing17, plumbing17)
pottyViz <- pottyViz %>%
	mutate(noPlumb_perc = no_plumb_estimate / housing_units_estimate * 100)

#read in geoJSON data to allow the mapping. I got the file <a href="https://eric.clst.org/tech/usgeojson/" target="_blank">from this site</a>. This makes a SpatialPolygonDataFrame, which is like if a list and a dataframe hooked up. We also need to subset down to just New Mexico counties.
counties <- geojson_read("us_counties_5m.geojson", what = "sp")
counties <- subset(counties, STATE == "35" & LSAD == "County")

#fix Doña Ana County — the way it came thru breaks the label code below.
counties$NAME[30] <- "Doña Ana"

#add the pottyViz dataframe so later the viz is married to the data correctly. this uses the `merge` function from package sp:: not the base one. This merges the one dataframe into the counties SpatialPolygonDataFrame, matched on the county name.
counties <- merge(counties, pottyViz, by.x = "NAME", by.y = "geog")

#create map function to pass values of noPlumb_perc to colors.
domain <- range(counties$noPlumb_perc)
pal <- colorNumeric(
  palette = "Blues",
  domain = domain)

#create labels -- this broke unless Doña Ana County was fixed above as the characters passed into the counties SpatialPolygonDataFrame included a \, so all the HTML was exposed. 
# sprintf uses C-style string formatting, which is the %s below. Originally the first two were %d, integer, and third was %g, double. But wrapping the display in format() below changes the display numbers into characters, so here we use %s, character.
labels <- sprintf(
  "<strong>%s %s</strong><br/>
  <br/>
  Estimated housing units 2017: %s<br/>
  Estimated units without plumbing: %s<br/>
  Percentage: %s<br/>
  <br/>
  <i>Source: Census Bureau American Community<br/>
   Survey 5-year estimates, 2017</i>",
  counties$NAME,
  counties$LSAD,
  format(counties$housing_units_estimate, big.mark = ","),
  format(counties$no_plumb_estimate, big.mark = ","),
  format(counties$noPlumb_perc, digits = 1)
  ) %>% lapply(htmltools::HTML)

#make the leaflet map
m <- leaflet(counties, options = leafletOptions(minZoom = 7, maxZoom = 8)) %>% 
setView(-106, 34.6, zoom = 7) %>% 
addProviderTiles(providers$CartoDB.Positron) %>% 
addPolygons(
	color = ~pal(counties$noPlumb_perc),
	weight = 1,
	opacity = 1,
	dashArray = 1,
	fillOpacity = 0.7, 
	highlight = highlightOptions(
		weight = 2,
		color = "black",
		dashArray = 1,
		fillOpacity = 0.7,
		bringToFront = T),
	label = labels,
	labelOptions = labelOptions(
    style = list("font-weight" = "normal", padding = "3px 8px"),
    textsize = "15px",
    offset = c(25,0), direction = "right")# tweak offset and direction to improve tooltip experience
	) %>% 
	addLegend(pal = pal, values = ~noPlumb_perc, opacity = 0.7, title = HTML("Estimated % of households<br/> without plumbing, 2017"), position = "bottomright")
  
#export as html page
saveWidget(m, file = "pottyMap.html")

#for a static image:
mapshot(m, file = "potty1.png")
