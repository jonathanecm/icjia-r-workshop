# template elements
<div class="header"></div>
<div class="footer"></div>
<img src="../images/icjia.png" class="logo"></img>
<img src="../images/il_seal.gif" class="seal"></img>
<div class="buttons">
<a href="../index.html">
  <button type="button">Home</button>
</a>
<a href="../modules.html">
  <button type="button">Modules</button>
</a>
<a href="../notes/module4_notes2.html">
  <button type="button">Notes</button>
</a>
</div>
<link href="https://fonts.googleapis.com/css?family=Oswald" rel="stylesheet">


# presentation
R Workshop
========================================================
type: slide-body
css: ../css/style_slides.css
<h3 style="color: #789">Module 4: Data visualization with R (2)</h3>  
2018-04-11  
Bobae Kang  
<small>(Bobae.Kang@illinois.gov)</small>  


Agenda
========================================================
<div style="text-align:center; margin-top:10%;">
<ul style="list-style: none">
<li>
  Part 1: The Grammar of Graphics</li>
<li style="color: #00061a; font-size: 1.1em; font-weight:700">
  Part 2: Maps and Interactive Plots</li>
</div>




========================================================
type:section
<img src="../images/caution.png" title="plot of chunk unnamed-chunk-2" alt="plot of chunk unnamed-chunk-2" width="50%" style="display: block; margin: auto; box-shadow: none;" />
<p style="font-size:0.5em; text-align: center; color: #777;">
Source: <a href="https://commons.wikimedia.org/wiki/File:DIN_4844-2_Warnung_vor_einer_Gefahrenstelle_D-W000.svg">Wikimedia Commons</a>
</p>


Maps
========================================================
type:section
<img src="module4_slides2-figure/unnamed-chunk-3-1.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" style="display: block; margin: auto; box-shadow: none;" />


Prerequisites
========================================================
* Shapefile
    * a popular format for storing geospatial data
* `rgdal` pacakge
    * an R interface to Geospatial Data Abstraction Library (GDAL) for reading and writing geospatial data formats
* Spatial objects in R


Shapefile
========================================================
> "A shapefile is a simple, nontopological format for storing the geometric location and attribute information of geographic features. Geographic features in a shapefile can be represented by points, lines, or polygons (areas)."<br> - "What is a shapefile?", *Esri*


> "The shapefile format is a popular geospatial vector data format for geographic information system (GIS) software [...] developed and regulated by Esri [...]. The shapefile format can spatially describe vector features: points, lines, and polygons [...]. Each item usually has attributes that describe it, such as name or temperature." - "Shapefile", *Wikipedia*


Shapefile components
========================================================
A shapefile format in fact consists of a collection of files. The following are commonly included when using shapefile data in R:


|File extension |Description                                                                    |
|:--------------|:------------------------------------------------------------------------------|
|`.shp`         |The main file that stores the feature geometry; *required*.                    |
|`.shx`         |The index file that stores the index of the feature geometry; *required*.      |
|`.dbf`         |The dBASE table that stores the attribute information of features; *required*. |
|`.prj`         |The file that stores the coordinate system information; used by ArcGIS.        |


Importing a shapefile
========================================================

```r
library(rgdal)
spatial_object <- readORG(dsn, layer)

# example:
# il_counties <- read(dsn = "shapefiles", layer = "il_counties")
```
* the `readORG` function imports a shapefile into R environment
    * `dsn` is the path to the directory with a shapefile to import
    * `layer` is the name of a shapefile to import 
* the output is a spatial vector object


Spatial (vector) objects in R
========================================================
* There are multiple spatial vector object types:
  * Without attributes: `Spatial*` classes
      * <small>`Points`, `MultiPoints`, `Pixels`, `Grid`, ``Lines`, `Polygons`</small>
  * With attributes: `Spatial*DataFrame` classes
      * The attributes `data.frame` table can be accessed using standard methods.
* See the ["Spatial Cheatsheet"](http://www.maths.lancs.ac.uk/~rowlings/Teaching/UseR2012/cheatsheet.html) for more on spatial objects in R


Example
========================================================

```r
class(counties)
```

```
[1] "SpatialPolygonsDataFrame"
attr(,"package")
[1] "sp"
```
* `icjiar` package provides a spatial object named `counties` for countes in Illinois
    * it is of the `SpatialPolygonsDataFrame` class


Packages for maps
========================================================
* `tmap` package for thematic maps
* `leaftlet` package for interactive maps
* And more


tmap: Thematic maps in R
========================================================
type: section
<img src="../images/tmap_worldmap.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" width="60%" style="display: block; margin: auto; box-shadow: none;" />
<p style="font-size:0.5em; text-align: center; color: #777;">
Source: <a href="https://github.com/mtennekes/tmap">tmap GitHub repo</a>
</p>


tmap
========================================================
> "With the tmap package, thematic maps can be generated with great flexibility. The syntax for creating plots is similar to that of ggplot2. The add-on package tmaptools contains tool functions for reading and processing shape files." - "tmap in a nutshell"

* In my experience, `tmap` is the most accessible R package for generating maps, making it easy to get visually appealing maps using data in the shapefile format.


The qtm() function
========================================================

```r
qtm(shape_object, ...)
```
* Generates a "quick thematic map"
    * comparable to `qplot()` in `ggplot2`
* Offers the same level of flexibility as the main plotting interface
    * The main interface is stil recommended for complex plots 


========================================================

```r
qtm(counties, fill = "circuit")
```

![plot of chunk unnamed-chunk-9](module4_slides2-figure/unnamed-chunk-9-1.png)


The tm_*() interface
========================================================

```r
tm_shape(shape_object) +
  tm_*() # add tmap elements as layers
```
* `tm_shape` takes a spatial "shape object"
* Add layers using tmap elements
    * two types of "drawing" layers: base and derived
    * "attribute" layers


========================================================
**Base `tmap` drawing layers**

|Drawing layer |Description     |Aesthetics       |
|:-------------|:---------------|:----------------|
|`tm_polygons` |Draws polygons  |col              |
|`tm_symbols`  |Draws symbols   |size, col, shape |
|`tm_lines`    |Draws lines     |col, lwd         |
|`tm_raster`   |Draws a raster  |col              |
|`tm_text`     |Add text labels |text, size, col  |
<p style="font-size:0.5em">The table is duplicated from a `tmap` <a href="https://cran.r-project.org/web/packages/tmap/vignettes/tmap-nutshell.html">vignette page</a></p>


========================================================
**Derived `tmap` drawing layers**

|Drawing layer |Description             |Aesthetics                     |
|:-------------|:-----------------------|:------------------------------|
|`tm_fill`     |Fills the polygons      |see `tm_polygons`              |
|`tm_borders`  |Draws polygon borders   |none                           |
|`tm_bubbles`  |Draws bubbles           |see `tm_symbols`               |
|`tm_squares`  |Draws squares           |see `tm_symbols`               |
|`tm_dots`     |Draws dots              |see `tm_symbols`               |
|`tm_markders` |Draws markers           |see `tm_symbols` and `tm_text` |
|`tm_iso`      |Draws iso/contour lines |see `tm_lines` and `tm_text`   |
<p style="font-size:0.5em">The table is duplicated from a `tmap` <a href="https://cran.r-project.org/web/packages/tmap/vignettes/tmap-nutshell.html">vignette page</a></p>


========================================================
**`tmap` attribute layers**

|Attribute layer |Description               |
|:---------------|:-------------------------|
|`tm_grid`       |Add coordinate grid lines |
|`tm_credits`    |Add credits text label    |
|`tm_compass`    |Add map compass           |
|`tm_scale_bar`  |Add scale bar             |
<p style="font-size:0.5em">The table is duplicated from a `tmap` <a href="https://cran.r-project.org/web/packages/tmap/vignettes/tmap-nutshell.html">vignette page</a></p>


========================================================

```r
tm_shape(counties) +
  tm_borders() +
  tm_fill(col = "circuit")
```

![plot of chunk unnamed-chunk-14](module4_slides2-figure/unnamed-chunk-14-1.png)


Layouts for maps
========================================================

```r
tm_layout(title = NA, scale = 1, title.size = 1.3, bg.color = "white", aes.color = c(fill = "grey85", borders = "grey40", symbols = "grey60", dots = "black", lines = "red", text = "black", na = "grey75"), ...)
```
* `tm_layout` is the general function to control all things about layout settings.
* `tm_style_*` functions which offer predefined sets of styling-related layout settings such as background colors, colors and font (similar to `ggplot2` themes).
* `tm_format_*` functions which offer predefined sets of position-related layout settings such as margins.


========================================================
**Predefined styles**

|Style                |Description                                                                    |
|:--------------------|:------------------------------------------------------------------------------|
|`tm_style_white`     |White background, commonly used colors (default)                               |
|`tm_style_gray`      |Gray background, useful to highlight sequential palettes (e.g. in choropleths) |
|`tm_style_natural`   |Emulation of natural view: blue waters and green land                          |
|`tm_style_bw`        |Greyscale                                                                      |
|`tm_style_classic`   |Classic styled maps                                                            |
|`tm_style_col_blind` |Style for colorblind viewers                                                   |
|`tm_style_cobalt`    |Inspired by latex beamer style cobalt                                          |
|`tm_style_albatross` |Inspired by latex beamer style albatross                                       |
|`tm_style_beaver`    |Inspired by latex beamer style beaver                                          |


========================================================

```r
tm_shape(counties) +
  tm_borders() +
  tm_fill(col = "circuit") +
  tm_style_classic()
```

![plot of chunk unnamed-chunk-17](module4_slides2-figure/unnamed-chunk-17-1.png)


========================================================
**Predefined formats**

|Format                  |Description                                                |
|:-----------------------|:----------------------------------------------------------|
|`tm_format_World`       |Format specified for world maps                            |
|`tm_format_World_wide`  |for world maps with more space for the legend              |
|`tm_format_Europe`      |for maps of Europe                                         |
|`tm_format_Europe_wide` |for maps of Europe with more space for the legend          |
|`tm_format_NLD`         |for maps of the Netherlands                                |
|`tm_format_NLD_wide`    |for maps of the Netherlands with more space for the legend |


========================================================

```r
tm_shape(counties) +
  tm_borders() +
  tm_fill(col = "circuit") +
  tm_format_World_wide()
```

![plot of chunk unnamed-chunk-19](module4_slides2-figure/unnamed-chunk-19-1.png)


Static vs interactive modes
========================================================

```r
tmap_mode("plot") # set to static "plot" mode
tmap_mode("view") # set to interactive "view" mode

ttmp() # toggle between modes
```

* "plot" mode generates a static map image
* "view" mode generates an interactive `leaflet` map
    * `tm_view()` is a function to specify options for "view" mode
    * `tmap_leaflet()` can directly generate an interactive `leaflet` map 

========================================================

```r
tmap_mode("view")
qtm(counties, fill = "circuit")
```

<iframe src="../interactive/tmap_view.html" style="display: block; margin: auto; min-height:500px; width:80%;"></iframe>


========================================================
type: section
<img src="../images/leaflet.png" title="plot of chunk unnamed-chunk-22" alt="plot of chunk unnamed-chunk-22" width="60%" style="display: block; margin: auto; margin-top: 15%; box-shadow: none;" />
<p style="font-size:0.5em; text-align: center; color: #777;">
Source: <a href="http://leafletjs.com/">leafletjs.com</a>
</p>


leaflet
========================================================
> "Leaflet is one of the most popular open-source JavaScript libraries for interactive maps. It's used by websites ranging from The New York Times and The Washington Post to GitHub and Flickr, as well as GIS specialists like OpenStreetMap, Mapbox, and CartoDB."<br>-"Leaflet for R", RStudio

* While `leaflet` is a powerful library for generating interactive maps, it takes much time and practice to get familiar with its API.
* Using `tmap`'s interactive view could be an alterantive to using `leaflet`'s API directly.


========================================================

```r
pal <- colorFactor(topo.colors(5), counties$circuit)
leaflet(counties) %>%
  addProviderTiles("CartoDB.Positron") %>%
  addPolygons(fillColor = ~pal(circuit), color = "darkgrey", weight = 2) %>%
  addLegend(pal = pal, values = ~circuit)
```

<iframe src="../interactive/leaflet.html" style="display: block; margin: auto; min-height:450px; width:80%;"></iframe>


Other options
========================================================
* base R plot
* `sp::spplot()`
* `ggplot2::geom_map()`
* `ggmap`


Resources
========================================================
* Eubank, N. (2015). ["Making maps in R"](http://www.nickeubank.com/wp-content/uploads/2015/10/RGIS3_MakingMaps_part1_mappingVectorData.html).
* Hijmans, R. (2016). ["Spatial Data Manipulation"](http://rspatial.org/spatial/index.html). *R Spatial*.
* `leaftlet` [official documentation page](https://rstudio.github.io/leaflet/)
* Lovelace, R. et al. (2017). ["Introduction to visualising spatial data in R
"](https://cran.r-project.org/doc/contrib/intro-spatial-rl.pdf).
* Pebesma, E. (n.d.). [*Simple Features for R*](http://r-spatial.github.io/sf/).
* `tmap` [github repository](https://github.com/mtennekes/tmap)
    * [Links to package vignettes and examples](https://github.com/mtennekes/tmap#vignettes-and-examples)


Interactive Plots
========================================================
type:section
<img src="../images/interactive.png" title="plot of chunk unnamed-chunk-24" alt="plot of chunk unnamed-chunk-24" width="25%" style="display: block; margin: auto; box-shadow: none;" />
<p style="font-size:0.5em; text-align:center; color: #777;">
Source: <a href="https://commons.wikimedia.org/wiki/File:Interactive_icon.svg">Wikipedia Commons</a>
</p>


Packages for interactive plots
========================================================
* `ggiraph`: an `htmlwidget` package for interactive `ggplot2` graphics
* `plotly`: R API for the plotly.js library
* `highcharter`: R API for the highchart.js library
* And more


========================================================
type: section
<img src="../images/ggiraph.gif" title="plot of chunk unnamed-chunk-25" alt="plot of chunk unnamed-chunk-25" width="30%" style="display: block; margin: auto; box-shadow: none;" />
<p style="font-size:0.5em; text-align: center; color: #777;">
Source: <a href="https://davidgohel.github.io/ggiraph/">ggiraph documentation page</a>
</p>


ggiraph
========================================================
> "ggiraph is an htmlwidget and a ggplot2 extension. It allows ggplot graphics to be animated."<br>- Gohel, D. (package author/creator)

`ggiraph` offers interactive "geoms" to be used for a `ggplot2` plot and renders the plot with interactive "geoms" as an interactive visualization.


Interactive geom layers
========================================================

```r
p <- plot + geom_*_interactive(...)
ggiraph(code = print(p), ...)
```
* `plot` is a `ggplot` object
* `ggiraph()` takes a `ggplot2::ggplot` object with an interactive "geom" to generate an interactive plot
* 12 interactive "geom" layers that can be integrated into a `ggplot` object:
    * bar, boxplot, histogram, line, map, path, point, polygon, rect, segment, text, tile


Interactive aesthetic mappings
========================================================

```r
aes(tooltip, onclick, data_id)
```
* Each interactive "geom" has mapping for the following interactive elements: 
  * `tooltip` is a column containing information to be displayed as tooltip
  * `onclick` is a column containing JavaScript instructions to run for a "click" event
  * `data_id` is a column containing id to be associated with elements.
      * Must be specified to use a customized "hover" effect


========================================================

```r
data <- ispcrime %>% filter(county != "Cook") %>% left_join(regions)
p <- ggplot(data, aes(x = violentCrime, propertyCrime, color = region)) +
  geom_point_interactive(aes(tooltip = county, data_id = county))
ggiraph(code = print(p), hover_css = "fill:orange;fill-opacity:.3;cursor:pointer;")
```

<iframe src="../interactive/ggiraph.html" style="display: block; margin: auto; min-height:500px; width:75%;"></iframe>


========================================================
type: section
<img src="../images/plotly.png" title="plot of chunk unnamed-chunk-29" alt="plot of chunk unnamed-chunk-29" width="35%" style="display: block; margin: auto; box-shadow: none;" />
<p style="font-size:0.5em; text-align: center; color: #777;">
Source: <a href="https://commons.wikimedia.org/wiki/File:Plotly_logo_for_digital_final_(6).png">wikimedia.org</a>
</p>


plotly
========================================================
> "Plotly's R graphing library makes interactive, publication-quality graphs online. Examples of how to make line plots, scatter plots, area charts, bar charts, error bars, box plots, histograms, heatmaps, subplots, multiple-axes, and 3D (WebGL based) charts."<br>- "Plotly R Library", *plotly*


The ggplotly() function
========================================================

```r
ggplotly(p = ggplot2::last_plot(), ...)
```
* `ggplotly()` provides a quick and easy way to convert a `ggplot` object into an interactive plotly object
* `p` is a `ggplot2::ggplot` object to be made interactive
    * The default value for `p` is the most recently created `ggplot` object if there is any
    

========================================================

```r
# using the same data
p <- ggplot(data, aes(violentCrime, propertyCrime, colour = region)) +
  geom_point() + labs(title = "Using ggplotly()")
ggplotly(p)
```

<iframe src="../interactive/ggplotly.html" style="display: block; margin: auto; min-height:500px; width:80%;"></iframe>


The plot_ly() interface
========================================================

```r
plot_ly(data, x, y, color, alpha, symbol, size, ...)

 # equivalent to add_type()
add_trace(p, ..., type = "type", inheret = TRUE)
```
* `plot_ly` takes a data frame and defines the aesthetic mappings
* "trace" is the "geom" equivalent in `plot_ly` interface, which adds a layer to the plot output
    * While `plot_ly` can define traces, using `add_trace()` and its variants make it possible to use a `dplyr`-style workflow with pipe operators
    * By default, each trace inherits the mappings from `p`


plotly add functions
========================================================

|Function          |Description             |Equivalent to `add_trace(...)`   |
|:-----------------|:-----------------------|:--------------------------------|
|`add_trace()`     |add traces with options |`NA`                             |
|`add_markers()`   |adds a scattorplot      |`type="scatter", mode="markers"` |
|`add_lines()`     |adds a line plot        |`type="scatter", mode="lines"`   |
|`add_bars()`      |adds a bar plot         |`type="bar", mode="markers"`     |
|`add_histogram()` |adds a histogram        |`type="histogram"`               |
|`add_boxplot()`   |adds a box plot         |`type="box"`                     |
|`add_pie()`       |adds a pie chart        |`type="", mode=""`               |
|`add_text()`      |adds texts              |                                 |
|`add_polygons()`  |adds polygons           |`type="", mode=""`               |


========================================================

```r
plot_ly(data, x = ~violentCrime, y = ~propertyCrime, color = ~region) %>%
  add_markers() %>%
  layout(title = "Using plot_ly() interface")
```

<iframe src="../interactive/plot_ly.html" style="display: block; margin: auto; min-height:500px; width:80%;"></iframe>



========================================================
type: section
<img src="../images/highcharter.png" title="plot of chunk unnamed-chunk-35" alt="plot of chunk unnamed-chunk-35" width="100%" style="display: block; margin: auto; margin-top: 15%; box-shadow: none;" />
<p style="font-size:0.5em; text-align: center; color: #777;">
Source: <a href="https://github.com/jbkunst/highcharter">highcharter github repo (jbkunst/highcharter)</a>
</p>


highcharter
========================================================
> "Highcharter is a R wrapper for Highcharts javascript libray and its modules. Highcharts is very mature and flexible javascript charting library and it has a great and powerful API."<br>-Kunst, J. (package author)

* We have already seen `Highcharts` plots in ICJIA R&A Unit's online articles (e.g. Figure 1 and Figure 2 in [this article](http://www.icjia.state.il.us/articles/evaluation-of-illinois-multi-jurisdictional-drug-task-forces)).


The hchart() function
========================================================

```r
hchart(data, type, hcaes(x, y, ...))
```
* Quickly generates a hichchart plot
    * Comparable to `qplot()` in `ggplot2`
* `type` specifies the type of plot (e.g. "scattor" for scattorplot)
* `hcaes()` works like `aes()` in `ggplot2`


========================================================

```r
# using the same data
hchart(data, type = "scatter", hcaes(x = violentCrime, y = propertyCrime, group = region)) %>%
  hc_title(text = "Using hchart() interface")
```

<iframe src="../interactive/hchart.html" style="display: block; margin: auto; min-height:500px; width:80%;"></iframe>


The highchart() interface
========================================================

```r
highchart() %>%
  hc_add_series(...) %>% # add a "series"
  hc_xAxis(...) %>% # define x-axis
  hc_yAxis(...) %>% # define y-axis
  hc_title(...) %>% # add the main title
  hc_chart(...) %>% # modify general plot options
  hc_color(...) %>% # control colors
  hc_*(...) # and more...
```
* Closely follows the original Javascript API


========================================================

```r
highchart() %>%
  hc_add_series(data, type = "scatter", hcaes(x = violentCrime, y = propertyCrime, group = region)) %>%
  hc_title(text = "Using highchart() interface")
```

<iframe src="../interactive/highchart.html" style="display: block; margin: auto; min-height:500px; width:80%;"></iframe>


Resources
========================================================
* `ggiraph` [official documentation page](https://davidgohel.github.io/ggiraph/articles/offcran/using_ggiraph.html)
* `highcharter` [official documentation page](http://jkunst.com/highcharter/index.html)
* `plotly` [official documentation page](https://plot.ly/r/)
* Sievert, C. (n.d.) [*`plotly` for R*](https://plotly-book.cpsievert.me/).


More on interactive plotting
========================================================
* `rAmCharts` is an R interface to the [`amCharts`](https://www.amcharts.com/products/) JavaScript library that offers interactive options for many common plot types and more.
* `chartjs` is an R interface to the [`Chart.js`](http://www.chartjs.org/) JavaScript library and offers six chart types (bar, line, pie, doughnut, radar, and polar area) for interactive plots.
* `googleVis` offers an R API to [Google Charts](https://developers.google.com/chart/), which offers rich set of interactive charts and data tools.
* `dygraphs` is an R interface to the [dygraphs](http://rstudio.github.io/dygraphs/index.html) JavaScript library for interactive time-series plots.


Other visualizations
========================================================
* [`visNetwork`](http://datastorm-open.github.io/visNetwork/) (wrapper for `vis.js`) and [`networkD3`](http://visjs.org/) are two popular packages for interactive visualization of network/graph data in R. Start with [`igraph`](http://igraph.org/r/) or [`tidygraph`](https://github.com/thomasp85/tidygraph) package to learn how to work with network objects in R
* [`wordcloud2`](https://cran.r-project.org/web/packages/wordcloud2/vignettes/wordcloud.html) (wrapper for `worldcloud2.js`) is a package for creating word clouds, a popular way to visualize text data.
* [`data.tree`](https://cran.r-project.org/web/packages/data.tree/vignettes/data.tree.html) is an R package for managing as well as visualizing hierarchical data and tree structures.


Questions?
========================================================
type: section
<img src="http://gifimage.net/wp-content/uploads/2017/10/david-pumpkins-gif.gif" title="plot of chunk unnamed-chunk-40" alt="plot of chunk unnamed-chunk-40" width="60%" style="display: block; margin: auto; box-shadow: none;" />
<p style="font-size:0.5em; text-align:center; color: #777;">
Source: <a href="http://gifimage.net/david-pumpkins-gif/">Gifimage.net</a>
</p>


========================================================
References
<ul style="font-size: 0.6em; list-style-type:none">
  <li>Gohel, D. (n.d.). <a href="https://davidgohel.github.io/ggiraph/index.html"><i>ggiraph</i></a>.</li>
  <li>Kunst, J. (2017). <a href="http://jkunst.com/highcharter/index.html"><i>HIGHCHARTER</i></a>.</li>
  <li>Sievert, C. (n.d.). <a href="https://plotly-book.cpsievert.me/"><i>plotly for R</i></a>.</li>
  <li>Tennekes, M. (n.d.). <a href="https://cran.r-project.org/web/packages/tmap/vignettes/tmap-nutshell.html">"tmap in a nutshell"</a>.</li>
  <li>Tennekes, M. (n.d.). <a href="https://cran.r-project.org/web/packages/tmap/vignettes/tmap-modes.html">"tmap modes: plot and interactive view"</a>.</li>
  <li>Reference manuals for the aforementioned R packages.</li>
</ul>
