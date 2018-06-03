---
title       : Developing Data Products
subtitle    : Week 4 Course Project
author      : primusdd
date        : 3-6-2018 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
github:
    user: primusdd
    repo: DDP-Week4
---

## Introduction

For the asignment I build an application that allows the user to filter cars based on MPG and the transmission. Selecting the filters in slider and checkboxes results in creating a subset of the mtcars dataset in R which is then shown in the UI. A sample of the dataset:


```r
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

---
## The UI Code

<style type="text/css">
code.r{ /* Code block */
    font-size: 12px;
}
</style>


```r
library(shiny)
library(plotly)

# Define UI for application that draws a histogram
shinyUI(fluidPage(
  
  # Application title
  titlePanel("Choosing a car based on MPG"),
  
  # Sidebar with a slider input for mpg, mpg values are shown as negatives to fit filtering on higher mileage
  sidebarLayout(
    sidebarPanel(
        p("This tool can be used to select a car from the mtcars data based on the mileage (or miles per gallon). You can select both the mpg you would like as well as filter on the transmission."),
        sliderInput("mpg", "miles per gallon:", min = -35, max = -10, value = -10),
       checkboxGroupInput("transmission","Transmission",c("Automatic"="0","Manual"="1"),c("0","1")),
       p("Created by Daniel Hubbeling for the Developing Data Products course on Coursera on 3 June 2018.")
       ),
    
    # Show the plots of the generated distribution and a table showing the cars in the selection
    mainPanel(
        h2("MPG vs. Weight"),
       plotlyOutput("wtPlot"),
       h2("MPG vs. Speed"),
       plotlyOutput("qsecPlot"),
       h2("Cars in selection"),
       dataTableOutput("carlist")
    )
  )
))
```


---
## The server side code



```r
library(shiny)
library(plotly)
library(dplyr)

# get the data to start with and add a label for transmission for easier reading
cardata<-mutate(mtcars, car.name = row.names(mtcars))
cardata$cyl<-as.factor(cardata$cyl)

# Define server logic required to draw a histogram
shinyServer(function(input, output) {

    #filter and sort the data to show
    cardata2<-reactive({
        cardata %>% filter(mpg >= -input$mpg) %>%  filter(am %in% input$transmission) %>% arrange(mpg)
    })
    
    # Make the output for the mpg vs weight plot
    output$wtPlot <- renderPlotly({
        inputdata<-cardata2()
        # plot the picture
        plot_ly(inputdata,x = ~wt, y = ~mpg,mode="markers" , color = ~hp, type="scatter",text=~car.name,title="MPG vs. Speed") 
      
    })
    
    # Make the output for the mpg vs speed plot
    output$qsecPlot <- renderPlotly({
        inputdata<-cardata2()
        # plot the picture
        plot_ly(inputdata,x = ~qsec, y = ~mpg,mode="markers" , color = ~hp, type="scatter",text=~car.name) 
        
    })
    
    # show a list of selected cars
    output$carlist<-renderDataTable({    
        inputdata<-cardata2()
        inputdata
    }) 
  
})
```

---
## Some links

Link to the application: https://primusdd.shinyapps.io/DDP-Week4/

Link to the GitHub repo: https://github.com/primusdd/DDP-Week4





