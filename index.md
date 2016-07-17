---
title       : Find the NBA player who scored the most in all-star games
subtitle    : 
author      : 
job         : 
framework   : html5slides        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Find the NBA player who scored the most in all-star games

```r
#### date: 
date()
```

```
## [1] "Sun Jul 17 14:23:41 2016"
```


---


## Introduction

#### This is to introduce an application that finds the player who scored the most in the NBA all-star games. The application can be found at [https://jqix.shinyapps.io/shiny_allstar/] (https://jqix.shinyapps.io/shiny_allstar/)

#### To use this application, choose the time period using selection input box for the beginning year and the ending year, and the page will show the player who had the highest score during that time period.

--- .class #id 

## server.R


```r
player <- read.csv("player_allstar.csv")
player$pts <- as.numeric(as.character(player$pts))

shinyServer(
  function(input, output) {
    getsubyear <- reactive ({
      subset(player, (year >= input$yearFrom & year <= input$yearTo))
    })
    
    output$name <- renderText({
      subyear <- getsubyear()
      highscore <- max(subyear$pts, na.rm=TRUE)
      maxPlayer <- subset(subyear, pts == highscore & !is.na(pts))
      paste(maxPlayer$firstname, maxPlayer$lastname)
    })

    output$score <- renderText({
      subyear <- getsubyear()
      max(subyear$pts, na.rm=TRUE)
    })
  }
)
```

---

## ui.R


```r
# Define UI for highest score player application
shinyUI(pageWithSidebar(
  headerPanel("Find the player with the highest score in NBA all star games"),
  sidebarPanel(
    selectInput("yearFrom", label = "From",
      choices = c(1950:2009),
      selected = 1950),
    selectInput("yearTo", label = "To",
      choices = c(1950:2009),
      selected = 2009)
  ),
  mainPanel(
    helpText("Data Source: www.databaseBasketball.com"),
    h3('Player Name: ', textOutput("name")),
    h3('Score: ', textOutput("score"))
  )
))
```

---

## Acknowledgement

#### The data set generously provided by [www.databaseBasketball.com] (www.databaseBasketball.com)
