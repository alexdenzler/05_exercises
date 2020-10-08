---
title: 'Weekly Exercises #5'
author: "Alex Denzler"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---





```r
library(tidyverse)     # for data cleaning and plotting
library(googlesheets4) # for reading googlesheet data
library(lubridate)     # for date manipulation
library(openintro)     # for the abbr2state() function
library(palmerpenguins)# for Palmer penguin data
library(maps)          # for map data
library(ggmap)         # for mapping points on maps
library(gplots)        # for col2hex() function
library(RColorBrewer)  # for color palettes
library(sf)            # for working with spatial data
library(leaflet)       # for highly customizable mapping
library(ggthemes)      # for more themes (including theme_map())
library(plotly)        # for the ggplotly() - basic interactivity
library(gganimate)     # for adding animation layers to ggplots
library(transformr)    # for "tweening" (gganimate)
library(shiny)         # for creating interactive apps
library(ggimage)
gs4_deauth()           # To not have to authorize each time you knit.
theme_set(theme_minimal())
```


```r
# SNCF Train data
small_trains <- read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-02-26/small_trains.csv") 

# Lisa's garden data
garden_harvest <- read_sheet("https://docs.google.com/spreadsheets/d/1DekSazCzKqPS2jnGhKue7tLxRU3GVL1oxi-4bEM5IWw/edit?usp=sharing") %>% 
  mutate(date = ymd(date))

# Lisa's Mallorca cycling data
mallorca_bike_day7 <- read_csv("https://www.dropbox.com/s/zc6jan4ltmjtvy0/mallorca_bike_day7.csv?dl=1") %>% 
  select(1:4, speed)

# Heather Lendway's Ironman 70.3 Pan Am championships Panama data
panama_swim <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_swim_20160131.csv")

panama_bike <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_bike_20160131.csv")

panama_run <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_run_20160131.csv")

#COVID-19 data from the New York Times
covid19 <- read_csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv")

beyonce_lyrics <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-29/beyonce_lyrics.csv')

taylor_swift_lyrics <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-29/taylor_swift_lyrics.csv')

sales <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-29/sales.csv')

charts <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-29/charts.csv')

census_pop_est_2018 <- read_csv("https://www.dropbox.com/s/6txwv3b4ng7pepe/us_census_2018_state_pop_est.csv?dl=1") %>% 
  separate(state, into = c("dot","state"), extra = "merge") %>% 
  select(-dot) %>% 
  mutate(state = str_to_lower(state))
```

## Put your homework on GitHub!

Go [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md) or to previous homework to remind yourself how to get set up. 

Once your repository is created, you should always open your **project** rather than just opening an .Rmd file. You can do that by either clicking on the .Rproj file in your repository folder on your computer. Or, by going to the upper right hand corner in R Studio and clicking the arrow next to where it says Project: (None). You should see your project come up in that list if you've used it recently. You could also go to File --> Open Project and navigate to your .Rproj file. 

## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* **NEW!!** With animated graphs, add `eval=FALSE` to the code chunk that creates the animation and saves it using `anim_save()`. Add another code chunk to reread the gif back into the file. See the [tutorial](https://animation-and-interactivity-in-r.netlify.app/) for help. 

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.

## Warm-up exercises from tutorial

  1. Choose 2 graphs you have created for ANY assignment in this class and add interactivity using the `ggplotly()` function.
  

```r
charts <- charts %>% 
  rename("country" = "chart")

saleChart <- sales %>% 
  left_join(charts, by = c("artist", "country", "released", "re_release", "label", "formats", "title")) %>% 
  filter(!country %in% c("World", "WW"))

saleChartPlot <- saleChart %>% 
  ggplot(aes(x = sales, y = reorder(title, sales), fill = country, color = artist)) + 
  geom_bar(stat = "identity", position = "stack") +
  scale_fill_manual(values = c("yellow", "pink", "turquoise3", "green", "red", "white", "gold")) + 
  scale_color_manual(values = c("blue", "purple")) +
  labs(title = "Sales by Album, Artist, and Country",
       x = "Sales",
       y = "Album",
       fill = "Country",
       color = "Artist",
       caption = "Alex Denzler")

garden_harvest_Lettuce <- garden_harvest %>% 
  filter(vegetable == "lettuce") %>% 
  ggplot(aes(y = fct_rev(fct_infreq(variety)))) + 
  geom_bar(fill = "green4", color = "black") +
  labs(title = "Frequency of Lettuce Harvest by Variety",
       x = "Count",
       y = "Variety")

ggplotly(saleChartPlot)
```

<!--html_preserve--><div id="htmlwidget-fc7973d228ce7f6b6efb" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-fc7973d228ce7f6b6efb">{"x":{"data":[{"orientation":"v","width":500000,"base":10.55,"x":[8039000],"y":[0.899999999999999],"text":"sales:  500000<br />reorder(title, sales): 0.9<br />country: AUS<br />artist: Taylor Swift","type":"bar","marker":{"autocolorscale":false,"color":"rgba(255,255,0,1)","line":{"width":1.88976377952756,"color":"rgba(160,32,240,1)"}},"name":"(AUS,Taylor Swift)","legendgroup":"(AUS,Taylor Swift)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[542000,80000,61000],"base":[5.55,2.55,0.55],"x":[8074200,2731000,1375455],"y":[0.9,0.9,0.9],"text":["sales:  542000<br />reorder(title, sales): 0.9<br />country: CAN<br />artist: Taylor Swift","sales:   80000<br />reorder(title, sales): 0.9<br />country: CAN<br />artist: Taylor Swift","sales:   61000<br />reorder(title, sales): 0.9<br />country: CAN<br />artist: Taylor Swift"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(255,192,203,1)","line":{"width":1.88976377952756,"color":"rgba(160,32,240,1)"}},"name":"(CAN,Taylor Swift)","legendgroup":"(CAN,Taylor Swift)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":30000,"base":1.55,"x":[1897000],"y":[0.9],"text":"sales:   30000<br />reorder(title, sales): 0.9<br />country: FR<br />artist: Beyoncé","type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,197,205,1)","line":{"width":1.88976377952756,"color":"rgba(0,0,255,1)"}},"name":"(FR,Beyoncé)","legendgroup":"(FR,Beyoncé)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[70000,13000,5700],"base":[5.55,2.55,0.55],"x":[7768200,2684500,1342105],"y":[0.9,0.9,0.9],"text":["sales:   70000<br />reorder(title, sales): 0.9<br />country: FRA<br />artist: Taylor Swift","sales:   13000<br />reorder(title, sales): 0.9<br />country: FRA<br />artist: Taylor Swift","sales:    5700<br />reorder(title, sales): 0.9<br />country: FRA<br />artist: Taylor Swift"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,255,0,1)","line":{"width":1.88976377952756,"color":"rgba(160,32,240,1)"}},"name":"(FRA,Taylor Swift)","legendgroup":"(FRA,Taylor Swift)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[256000,268200,32601],"base":[6.55,5.55,0.55],"x":[5286000,7599100,1322954.5],"y":[0.9,0.9,0.9],"text":["sales:  256000<br />reorder(title, sales): 0.9<br />country: JPN<br />artist: Taylor Swift","sales:  268200<br />reorder(title, sales): 0.9<br />country: JPN<br />artist: Taylor Swift","sales:   32601<br />reorder(title, sales): 0.9<br />country: JPN<br />artist: Taylor Swift"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","line":{"width":1.88976377952756,"color":"rgba(160,32,240,1)"}},"name":"(JPN,Taylor Swift)","legendgroup":"(JPN,Taylor Swift)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[1260000,700000,1740000,791000,418000,328000],"base":[11.55,7.55,9.55,3.55,4.55,1.55],"x":[5730000,3960000,4250000,1895500,2721000,1718000],"y":[0.899999999999999,0.899999999999999,0.899999999999999,0.9,0.9,0.9],"text":["sales: 1260000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Beyoncé","sales:  700000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Beyoncé","sales: 1740000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Beyoncé","sales:  791000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Beyoncé","sales:  418000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Beyoncé","sales:  328000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Beyoncé"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(255,255,255,1)","line":{"width":1.88976377952756,"color":"rgba(0,0,255,1)"}},"name":"(UK,Beyoncé)","legendgroup":"(UK,Beyoncé)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[609000,169281,693000,1250000,378000,221654],"base":[10.55,8.55,6.55,5.55,2.55,0.55],"x":[7484500,4778640.5,4811500,6840000,2489000,1195827],"y":[0.899999999999999,0.899999999999999,0.9,0.9,0.9,0.9],"text":["sales:  609000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Taylor Swift","sales:  169281<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Taylor Swift","sales:  693000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Taylor Swift","sales: 1250000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Taylor Swift","sales:  378000<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Taylor Swift","sales:  221654<br />reorder(title, sales): 0.9<br />country: UK<br />artist: Taylor Swift"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(255,255,255,1)","line":{"width":1.88976377952756,"color":"rgba(160,32,240,1)"}},"name":"(UK,Taylor Swift)","legendgroup":"(UK,Taylor Swift)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[5100000,3610000,3380000,1500000,2512000,1554000],"base":[11.55,7.55,9.55,3.55,4.55,1.55],"x":[2550000,1805000,1690000,750000,1256000,777000],"y":[0.899999999999999,0.899999999999999,0.899999999999999,0.9,0.9,0.9],"text":["sales: 5100000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Beyoncé","sales: 3610000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Beyoncé","sales: 3380000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Beyoncé","sales: 1500000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Beyoncé","sales: 2512000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Beyoncé","sales: 1554000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Beyoncé"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(255,215,0,1)","line":{"width":1.88976377952756,"color":"rgba(0,0,255,1)"}},"name":"(US,Beyoncé)","legendgroup":"(US,Beyoncé)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[5720000,7180000,4694000,4465000,6215000,2300000,1085000],"base":[12.55,10.55,8.55,6.55,5.55,2.55,0.55],"x":[2860000,3590000,2347000,2232500,3107500,1150000,542500],"y":[0.899999999999999,0.899999999999999,0.899999999999999,0.9,0.9,0.9,0.9],"text":["sales: 5720000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Taylor Swift","sales: 7180000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Taylor Swift","sales: 4694000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Taylor Swift","sales: 4465000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Taylor Swift","sales: 6215000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Taylor Swift","sales: 2300000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Taylor Swift","sales: 1085000<br />reorder(title, sales): 0.9<br />country: US<br />artist: Taylor Swift"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(255,215,0,1)","line":{"width":1.88976377952756,"color":"rgba(160,32,240,1)"}},"name":"(US,Taylor Swift)","legendgroup":"(US,Taylor Swift)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":142.465753424658},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"Sales by Album, Artist, and Country","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-417260,8762460],"tickmode":"array","ticktext":["0e+00","2e+06","4e+06","6e+06","8e+06"],"tickvals":[0,2000000,4000000,6000000,8000000],"categoryorder":"array","categoryarray":["0e+00","2e+06","4e+06","6e+06","8e+06"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"Sales","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,14.6],"tickmode":"array","ticktext":["Lover","Lemonade","Reputation","4","Beyoncé","1989","Red","B'Day","Speak Now","I Am... Sasha Fierce","Fearless","Dangerously in Love","Taylor Swift","Folklore"],"tickvals":[1,2,3,4,5,6,7,8,9,10,11,12,13,14],"categoryorder":"array","categoryarray":["Lover","Lemonade","Reputation","4","Beyoncé","1989","Red","B'Day","Speak Now","I Am... Sasha Fierce","Fearless","Dangerously in Love","Taylor Swift","Folklore"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"Album","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"y":0.826771653543307},"annotations":[{"text":"Country<br />Artist","x":1.02,"y":1,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"left","yanchor":"bottom","legendTitle":true}],"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"6f536552854b":{"x":{},"y":{},"fill":{},"colour":{},"type":"bar"}},"cur_data":"6f536552854b","visdat":{"6f536552854b":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
ggplotly(garden_harvest_Lettuce)
```

<!--html_preserve--><div id="htmlwidget-839f2dfa407e8c6bc8fb" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-839f2dfa407e8c6bc8fb">{"x":{"data":[{"orientation":"v","width":[1,3,9,27,28],"base":[0.55,1.55,2.55,3.55,4.55],"x":[0.5,1.5,4.5,13.5,14],"y":[0.9,0.9,0.9,0.9,0.9],"text":["count:  1<br />fct_rev(fct_infreq(variety)): 0.9","count:  3<br />fct_rev(fct_infreq(variety)): 0.9","count:  9<br />fct_rev(fct_infreq(variety)): 0.9","count: 27<br />fct_rev(fct_infreq(variety)): 0.9","count: 28<br />fct_rev(fct_infreq(variety)): 0.9"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,139,0,1)","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)"}},"showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":148.310502283105},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"Frequency of Lettuce Harvest by Variety","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-1.4,29.4],"tickmode":"array","ticktext":["0","10","20"],"tickvals":[0,10,20],"categoryorder":"array","categoryarray":["0","10","20"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"Count","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,5.6],"tickmode":"array","ticktext":["mustard greens","reseed","Tatsoi","Farmer's Market Blend","Lettuce Mixture"],"tickvals":[1,2,3,4,5],"categoryorder":"array","categoryarray":["mustard greens","reseed","Tatsoi","Farmer's Market Blend","Lettuce Mixture"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"Variety","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":false,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"6f539ada959":{"y":{},"type":"bar"}},"cur_data":"6f539ada959","visdat":{"6f539ada959":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
  
  
  2. Use animation to tell an interesting story with the `small_trains` dataset that contains data from the SNCF (National Society of French Railways). These are Tidy Tuesday data! Read more about it [here](https://github.com/rfordatascience/tidytuesday/tree/master/data/2019/2019-02-26).


```r
small_trains_late <- small_trains %>% 
  filter(delayed_number > .15) %>% 
  mutate(prop_late = num_arriving_late/total_num_trips) %>% 
  drop_na(service)

small_trains_late %>% 
  ggplot(aes(x = num_arriving_late, y = factor(month), fill = service)) + 
  geom_violin() + 
  labs(title = "Distribution of Late Arrivals",
       x = "Number of Late Arrivals",
       y = "Month",
       fill = "Service Type")
```

![](05_exercises_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

## Garden data

  3. In this exercise, you will create a stacked area plot that reveals itself over time (see the `geom_area()` examples [here](https://ggplot2.tidyverse.org/reference/position_stack.html)). You will look at cumulative harvest of tomato varieties over time. You should do the following:
  * From the `garden_harvest` data, filter the data to the tomatoes and find the *daily* harvest in pounds for each variety.  
  * Then, for each variety, find the cumulative harvest in pounds.  
  * Use the data you just made to create a static cumulative harvest area plot, with the areas filled with different colors for each vegetable and arranged (HINT: `fct_reorder()`) from most to least harvested (most on the bottom).  
  * Add animation to reveal the plot over date. 
  

```r
garden_harvestT <- garden_harvest %>% 
  filter(vegetable == "tomatoes") %>% 
  group_by(variety, date) %>% 
  summarize(daily_harvest = sum(weight)*0.00220462) %>% 
  mutate(day_of_week = wday(date, label = TRUE)) %>% 
  ungroup() %>% 
  mutate(variety = fct_reorder(variety, daily_harvest, sum, .desc = TRUE), 
         cumHarv = cumsum(daily_harvest)) 

garden_harvestT %>% 
  ggplot(aes(x = date, y = cumHarv, fill = variety)) + 
  geom_area(position = "stack", alpha = .6) + 
  labs(title = "Harvest Area Plot by Variety",
       x = "Date",
       y = "Cumulative Harvest",
       fill = "Variety") +
  transition_reveal(date)
```

![](05_exercises_files/figure-html/tomato_date-1.gif)<!-- -->


```r
anim_save("tomato_date.gif")
```

  
  

## Maps, animation, and movement!

  4. Map my `mallorca_bike_day7` bike ride using animation! 
  Requirements:
  * Plot on a map using `ggmap`.  
  * Show "current" location with a red point. 
  * Show path up until the current point.  
  * Color the path according to elevation.  
  * Show the time in the subtitle.  
  * CHALLENGE: use the `ggimage` package and `geom_image` to add a bike image instead of a red point. You can use [this](https://raw.githubusercontent.com/llendway/animation_and_interactivity/master/bike.png) image. See [here](https://goodekat.github.io/presentations/2019-isugg-gganimate-spooky/slides.html#35) for an example. 
  * Add something of your own! And comment on if you prefer this to the static map and why or why not.
  

```r
mallorca <- get_stamenmap(
    bbox = c(left = 2.3, bottom = 39.52, right = 2.73, top = 39.713), 
    maptype = "terrain",
    zoom = 12)

mallorca_bike_day7 <- mallorca_bike_day7 %>% 
  mutate(image = "https://raw.githubusercontent.com/llendway/animation_and_interactivity/master/bike.png")

ggmap(mallorca) +
  geom_point(data = mallorca_bike_day7, 
             aes(x = lon, y = lat), size = .5, color = "red") + 
  geom_path(data = mallorca_bike_day7, aes(x = lon, y = lat, color = ele), size = 1.7) + 
  geom_image(data = mallorca_bike_day7, aes(x = lon, y = lat, image = image), size = .1) +
  geom_text(data = mallorca_bike_day7, aes(label = speed)) +
  scale_color_viridis_c(option = "plasma") +
  theme_map() + 
  theme(legend.background = element_blank()) + 
  labs(title = "Lisa's Bike Path in Mallorca",
       subtitle = "Time: {frame_along}",
       color = "Elevation") + 
  transition_reveal(time) + 
  exit_recolor(color = "black")
```

![](05_exercises_files/figure-html/unnamed-chunk-4-1.gif)<!-- -->
  
  - I prefer this map because it is clearer than a static map, as we can see the starting point and ending point without any ambiguity. It's also a lot more fun!
  
  
  5. In this exercise, you get to meet my sister, Heather! She is a proud Mac grad, currently works as a Data Scientist at 3M where she uses R everyday, and for a few years (while still holding a full-time job) she was a pro triathlete. You are going to map one of her races. The data from each discipline of the Ironman 70.3 Pan Am championships, Panama is in a separate file - `panama_swim`, `panama_bike`, and `panama_run`. Create a similar map to the one you created with my cycling data. You will need to make some small changes: 1. combine the files (HINT: `bind_rows()`, 2. make the leading dot a different color depending on the event (for an extra challenge, make it a different image using `geom_image()!), 3. CHALLENGE (optional): color by speed, which you will need to compute on your own from the data. You can read Heather's race report [here](https://heatherlendway.com/2016/02/10/ironman-70-3-pan-american-championships-panama-race-report/). She is also in the Macalester Athletics [Hall of Fame](https://athletics.macalester.edu/honors/hall-of-fame/heather-lendway/184) and still has records at the pool. 
  

```r
panama_total <- bind_rows(panama_bike, panama_run, panama_swim) %>% 
  arrange(time)

panama <- get_stamenmap(
    bbox = c(left = -79.6114, bottom = 8.8944, right = -79.4241, top = 8.99), 
    maptype = "terrain",
    zoom = 15)

ggmap(panama) +
  geom_point(data = panama_total, aes(x = lon, y = lat), size = .8, color = "red") + 
  geom_path(data = panama_total, aes(x = lon, y = lat, color = ele), size = 1.7) + 
  scale_color_viridis_c(option = "plasma") +
  theme_map() + 
  theme(legend.background = element_blank()) + 
  labs(title = "Heather's Triathalon Path in Panama",
       subtitle = "Time: {frame_along}",
       color = "Elevation") + 
  transition_reveal(time) + 
  exit_recolor(color = "black")
```

![](05_exercises_files/figure-html/unnamed-chunk-5-1.gif)<!-- -->
  
## COVID-19 data

  6. In this exercise, you are going to replicate many of the features in [this](https://aatishb.com/covidtrends/?region=US) visualization by Aitish Bhatia but include all US states. Requirements:
 * Create a new variable that computes the number of new cases in the past week (HINT: use the `lag()` function you've used in a previous set of exercises). Replace missing values with 0's using `replace_na()`.  
  * Filter the data to omit rows where the cumulative case counts are less than 20.  
  * Create a static plot with cumulative cases on the x-axis and new cases in the past 7 days on the x-axis. Connect the points for each state over time. HINTS: use `geom_path()` and add a `group` aesthetic.  Put the x and y axis on the log scale and make the tick labels look nice - `scales::comma` is one option. This plot will look pretty ugly as is.
  * Animate the plot to reveal the pattern by date. Display the date as the subtitle. Add a leading point to each state's line (`geom_point()`) and add the state name as a label (`geom_text()` - you should look at the `check_overlap` argument).  
  * Use the `animate()` function to have 200 frames in your animation and make it 30 seconds long. 
  * Comment on what you observe.
  

```r
covid19 <- covid19 %>% 
  group_by(state) %>% 
  mutate(lagDay7 = lag(cases, 7, order_by = date), totalCases = cumsum(cases)) %>% 
  replace_na(list(lagDay7 = 0)) %>% 
  mutate(casesPastWeek = cases - lagDay7) %>%          
  filter(cases >= 20)

weeklyCovid <- covid19 %>% 
  ggplot(aes(x = totalCases, y = casesPastWeek, group = state)) +
  geom_point() +
  geom_path() + 
  geom_text(aes(label = state), check_overlap = TRUE) +
  scale_x_log10(label = scales::comma) + 
  scale_y_log10(label = scales::comma) +
  labs(title = "Week to Week Covid Cases by State",
       subtitle = "Date: {frame_along}",
       x = "Total Cases",
       y = "Weekly Cases") + 
  theme(legend.position = "none") +
  transition_reveal(date)

animate(weeklyCovid, nframes = 200, duration = 30)
```

![](05_exercises_files/figure-html/unnamed-chunk-6-1.gif)<!-- -->


  - Here, we are able to observe the trends of each individual state, and how each state did with "flattening the curve". Specifically, we can see how New York and California were hit hard by covid early, while Texas and Florida were hit hard later. The rest of the data are pretty difficult to observe, simply because of the overlapping of lines and names. 

  
  7. In this exercise you will animate a map of the US, showing how cumulative COVID-19 cases per 10,000 residents has changed over time. This is similar to exercises 11 & 12 from the previous exercises, with the added animation! So, in the end, you should have something like the static map you made there, but animated over all the days. Put date in the subtitle. Comment on what you see.
  

```r
states_map <- map_data("state")

covid19Tot <- covid19 %>% 
  group_by(state, date) %>% 
  mutate(state = str_to_lower(state)) %>% 
  left_join(census_pop_est_2018,
            by = "state") %>% 
  mutate(casesPer10000 = (cases/est_pop_2018)*10000) 
  
covid19Map <- covid19Tot %>% 
  ggplot() +
  geom_map(map = states_map,
           aes(map_id = state,
               fill = casesPer10000, group = date)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  scale_fill_gradient(low = "blue", high = "red") +
  theme(legend.background = element_blank()) +
  theme_map() + 
  labs(title = "Covid Cases by State",
       subtitle = "Date: {frame_along}",
       fill = "Cases per 10,000") +
  transition_reveal(date)

animate(covid19Map, nframes = 200, duration = 30)
```

![](05_exercises_files/figure-html/covidMap-1.gif)<!-- -->

```r
anim_save("covidMap.gif")
```
  
  
  - We can see that all states appear by the end of March. Next, we see around April 1st, New York brightens up significantly, while other states also brighten up. For the rest of the time, there is not too much of a change in color except in Florida and Texas. 

## Your first `shiny` app

  8. This app will also use the COVID data. Make sure you load that data and all the libraries you need in the `app.R` file you create. Below, you will post a link to the app that you publish on shinyapps.io. You will create an app to compare states' cumulative number of COVID cases over time. The x-axis will be number of days since 20+ cases and the y-axis will be cumulative cases on the log scale (`scale_y_log10()`). We use number of days since 20+ cases on the x-axis so we can make better comparisons of the curve trajectories. You will have an input box where the user can choose which states to compare (`selectInput()`) and have a submit button to click once the user has chosen all states they're interested in comparing. The graph should display a different line for each state, with labels either on the graph or in a legend. Color can be used if needed. 
  
## GitHub link

  9. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 05_exercises.Rmd, provide a link to the 05_exercises.md file, which is the one that will be most readable on GitHub. If that file isn't very readable, then provide a link to your main GitHub page.
  
  [https://github.com/alexdenzler/05_exercises/blob/master/05_exercises.md](https://github.com/alexdenzler/05_exercises/blob/master/05_exercises.md)


**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
