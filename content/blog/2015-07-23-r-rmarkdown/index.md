---
title: "Test Post"
author: "Katie Press"
date: 2021-06-14T21:13:14-05:00
categories: ["R"]
draft: true
menu: main
tags: ["R Markdown", "plot", "regression"]
output: 
  blogdown::html_page: 
    keep_md: true
    df_print: paged
---



# R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

You can embed an R code chunk like this:


```r
summary(cars)
##      speed           dist       
##  Min.   : 4.0   Min.   :  2.00  
##  1st Qu.:12.0   1st Qu.: 26.00  
##  Median :15.0   Median : 36.00  
##  Mean   :15.4   Mean   : 42.98  
##  3rd Qu.:19.0   3rd Qu.: 56.00  
##  Max.   :25.0   Max.   :120.00
fit <- lm(dist ~ speed, data = cars)
fit
## 
## Call:
## lm(formula = dist ~ speed, data = cars)
## 
## Coefficients:
## (Intercept)        speed  
##     -17.579        3.932
```



```r

callback <- c(
  "$('table.dataTable.display tbody tr:odd').css('background-color', '#e9e8eb');",
  "$('table.dataTable.display tbody tr:even').css('background-color', '#f4edff');"
)

tab <- DT::datatable(cars, 
              rownames = FALSE, 
  extensions = 'Scroller', 
  options = list(
  deferRender = TRUE,
  scrollY = 200,
  scroller = TRUE,
  scrollX = TRUE,
  dom = 't',
  style = 'bootstrap',
  class = 'table-bordered table-condensed',
  initComplete = DT::JS(
    "function(settings, json) {",
    "$(this.api().table().header()).css({'background-color': '#232928', 'color': '#fff'});",
    "}")
),
  callback = DT::JS(callback))

widgetframe::frameWidget(tab)
```

```{=html}
<div id="htmlwidget-3a51a865e60f4c9251f9" style="width:100%;height:480px;" class="widgetframe html-widget"></div>
<script type="application/json" data-for="htmlwidget-3a51a865e60f4c9251f9">{"x":{"url":"index_files/figure-html//widgets/widget_unnamed-chunk-1.html","options":{"xdomain":"*","allowfullscreen":false,"lazyload":false}},"evals":[],"jsHooks":[]}</script>
```

[*The Smartest Guys In The Room*](https://www.amazon.com/dp/B00EOAS0EK/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)


# Including Plots

You can also embed plots. See Figure \@ref(fig:pie) for example:


```r
par(mar = c(0, 1, 0, 1))
pie(
  c(280, 60, 20),
  c('Sky', 'Sunny side of pyramid', 'Shady side of pyramid'),
  col = c('#0292D8', '#F7EA39', '#C4B632'),
  init.angle = -50, border = NA
)
```

<div class="figure">
<img src="index_files/figure-html/pie-1.png" alt="A fancy pie chart." width="672" />
<p class="caption">(\#fig:pie)A fancy pie chart.</p>
</div>
