---
title: The Truth Is Out There
author: "Katie Press"
date: 2021-01-02T21:13:14-05:00
categories: ["R"]
tags: ["R Markdown", "plot", "regression"]
output: 
  blogdown::html_page: 
    keep_md: true
    df_print: paged
---





I'm a huge X-Files fan. I also love working with text data. I've had an idea floating around for a while to do a tidy text analysis on X-Files episode scripts. The X-Files has been around long enough that there are tons of fandom sites, and you can easily find transcripts of the original 9 seasons.First, I wanted to get some basic information about the episodes, so that's what this post will focus on. My first thought was to go to Wikipedia. There is a page with tables for each season and I can use that as the base URL for scraping. 

Packages used in this first episode:

- Tidyverse, obviously. This is always the first package I load. 
- Janitor, which has a couple of functions I like to use, especially clean_names() to clean and remove special characters from column names in new datasets. 
- Rvest, which can be used to scrape data from websites. 
- Googlesheets4, which is an update of the original googlesheets package. I can use this to store my data because I have more than one computer I use on a regular basis.  
- Extrafont (pretty self-explanatory).
- Ggiraph for graph animation.

Now, on to the X-Files.


```r
wiki <- "https://en.wikipedia.org/wiki/List_of_The_X-Files_episodes"
```

To find out what selector you need to look at the tables of interest, you can use a Chrome extension called SelectorGadget, or you can just right click on the specific spot on a website and choose "inspect" in the dropdown menu that comes up - which is what I usually do.

In this case it's pretty easy, the html nodes I'm interested in are simply "table" class. At first glance, it looks like tables 2 through 14 are "wiki episode table". That's one more than I would expect, because there are nine original seasons, and then two follow-up seasons that came out more recently (10-11). However, there are also two X-Files movies, which appear to have separate tables. I don't want to deal with those right now really, so I will leave them out when I scrape the tables. 


```r
wiki %>% 
  read_html() %>% 
  html_nodes(., "table")
## {xml_nodeset (19)}
##  [1] <table class="wikitable plainrowheaders" style="text-align:center"><tbod ...
##  [2] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
##  [3] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
##  [4] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
##  [5] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
##  [6] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
##  [7] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
##  [8] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
##  [9] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
## [10] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
## [11] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
## [12] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
## [13] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
## [14] <table class="wikitable plainrowheaders wikiepisodetable" style="width:1 ...
## [15] <table role="presentation" class="mbox-small plainlinks sistersitebox" s ...
## [16] <table class="nowraplinks hlist mw-collapsible autocollapse navbox-inner ...
## [17] <table class="nowraplinks navbox-subgroup" style="border-spacing:0"><tbo ...
## [18] <table class="nowraplinks navbox-subgroup" style="border-spacing:0"><tbo ...
## [19] <table class="nowraplinks hlist mw-collapsible autocollapse navbox-inner ...
```

So before we get the tables, I'm just going to select the nodes I actually want to collect, then use html_table to gather them all in table format.


```r
tables <- wiki %>% 
  read_html() %>% 
  html_nodes(., "table") %>% 
  .[c(2:6, 8:11, 13:14)] %>% 
  html_table(fill = TRUE)
```

I won't show all the tables just for the sake of space, but here is the first one. It looks like the "prod code" column is going to cause issues when I try and map them to one dataframe, because in some cases there are hyperlinks which results in inconsistent column names. (Hint: the table is interactive so you can flip over to the other columns or down to the next page). 


```r
tables[[1]]
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["No.overall"],"name":[1],"type":["int"],"align":["right"]},{"label":["No. inseason"],"name":[2],"type":["int"],"align":["right"]},{"label":["Title"],"name":[3],"type":["chr"],"align":["left"]},{"label":["Directed by"],"name":[4],"type":["chr"],"align":["left"]},{"label":["Written by"],"name":[5],"type":["chr"],"align":["left"]},{"label":["Original air date"],"name":[6],"type":["chr"],"align":["left"]},{"label":["Prod.code [40]"],"name":[7],"type":["chr"],"align":["left"]},{"label":["U.S. viewers(millions)"],"name":[8],"type":["chr"],"align":["left"]}],"data":[{"1":"1","2":"1","3":"\"Pilot\"‡","4":"Robert Mandel","5":"Chris Carter","6":"September 10, 1993 (1993-09-10)","7":"1X79","8":"12.0[41]"},{"1":"2","2":"2","3":"\"Deep Throat\"‡","4":"Daniel Sackheim","5":"Chris Carter","6":"September 17, 1993 (1993-09-17)","7":"1X01","8":"11.1[42]"},{"1":"3","2":"3","3":"\"Squeeze\"","4":"Harry Longstreet","5":"Glen Morgan & James Wong","6":"September 24, 1993 (1993-09-24)","7":"1X02","8":"11.1[43]"},{"1":"4","2":"4","3":"\"Conduit\"","4":"Daniel Sackheim","5":"Alex Gansa & Howard Gordon","6":"October 1, 1993 (1993-10-01)","7":"1X03","8":"9.2[44]"},{"1":"5","2":"5","3":"\"The Jersey Devil\"","4":"Joe Napolitano","5":"Chris Carter","6":"October 8, 1993 (1993-10-08)","7":"1X04","8":"10.4[45]"},{"1":"6","2":"6","3":"\"Shadows\"","4":"Michael Katleman","5":"Glen Morgan & James Wong","6":"October 22, 1993 (1993-10-22)","7":"1X05","8":"8.8[46]"},{"1":"7","2":"7","3":"\"Ghost in the Machine\"","4":"Jerrold Freedman","5":"Alex Gansa & Howard Gordon","6":"October 29, 1993 (1993-10-29)","7":"1X06","8":"9.5[47]"},{"1":"8","2":"8","3":"\"Ice\"","4":"David Nutter","5":"Glen Morgan & James Wong","6":"November 5, 1993 (1993-11-05)","7":"1X07","8":"10.0[48]"},{"1":"9","2":"9","3":"\"Space\"","4":"William Graham","5":"Chris Carter","6":"November 12, 1993 (1993-11-12)","7":"1X08","8":"10.7[49]"},{"1":"10","2":"10","3":"\"Fallen Angel\"‡","4":"Larry Shaw","5":"Howard Gordon & Alex Gansa","6":"November 19, 1993 (1993-11-19)","7":"1X09","8":"8.8[50]"},{"1":"11","2":"11","3":"\"Eve\"","4":"Fred Gerber","5":"Kenneth Biller & Chris Brancato","6":"December 10, 1993 (1993-12-10)","7":"1X10","8":"10.4[51]"},{"1":"12","2":"12","3":"\"Fire\"","4":"Larry Shaw","5":"Chris Carter","6":"December 17, 1993 (1993-12-17)","7":"1X11","8":"11.1[52]"},{"1":"13","2":"13","3":"\"Beyond the Sea\"","4":"David Nutter","5":"Glen Morgan & James Wong","6":"January 7, 1994 (1994-01-07)","7":"1X12","8":"10.8[53]"},{"1":"14","2":"14","3":"\"Gender Bender\"","4":"Rob Bowman","5":"Larry Barber & Paul Barber","6":"January 21, 1994 (1994-01-21)","7":"1X13","8":"11.1[54]"},{"1":"15","2":"15","3":"\"Lazarus\"","4":"David Nutter","5":"Alex Gansa & Howard Gordon","6":"February 4, 1994 (1994-02-04)","7":"1X14","8":"12.1[55]"},{"1":"16","2":"16","3":"\"Young at Heart\"","4":"Michael Lange","5":"Scott Kaufer and Chris Carter","6":"February 11, 1994 (1994-02-11)","7":"1X15","8":"11.5[56]"},{"1":"17","2":"17","3":"\"E.B.E.\"‡","4":"William Graham","5":"Glen Morgan & James Wong","6":"February 18, 1994 (1994-02-18)","7":"1X16","8":"N/A"},{"1":"18","2":"18","3":"\"Miracle Man\"","4":"Michael Lange","5":"Chris Carter & Howard Gordon","6":"March 18, 1994 (1994-03-18)","7":"1X17","8":"11.6[57]"},{"1":"19","2":"19","3":"\"Shapes\"","4":"David Nutter","5":"Marilyn Osborn","6":"April 1, 1994 (1994-04-01)","7":"1X18","8":"11.5[58]"},{"1":"20","2":"20","3":"\"Darkness Falls\"","4":"Joe Napolitano","5":"Chris Carter","6":"April 15, 1994 (1994-04-15)","7":"1X19","8":"12.5[59]"},{"1":"21","2":"21","3":"\"Tooms\"","4":"David Nutter","5":"Glen Morgan & James Wong","6":"April 22, 1994 (1994-04-22)","7":"1X20","8":"13.4[60]"},{"1":"22","2":"22","3":"\"Born Again\"","4":"Jerrold Freedman","5":"Howard Gordon & Alex Gansa","6":"April 29, 1994 (1994-04-29)","7":"1X21","8":"13.7[61]"},{"1":"23","2":"23","3":"\"Roland\"","4":"David Nutter","5":"Chris Ruppenthal","6":"May 6, 1994 (1994-05-06)","7":"1X22","8":"12.5[62]"},{"1":"24","2":"24","3":"\"The Erlenmeyer Flask\"‡","4":"R. W. Goodwin","5":"Chris Carter","6":"May 13, 1994 (1994-05-13)","7":"1X23","8":"14.0[63]"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

I'm going to add names to this list of dataframes before I clean the column names.


```r
names(tables) <- rep(paste0("Season ", 1:11))
```

Now to get the column names. They are all the same aside from the issue I mentioned earlier. 

```r
names(tables[[1]] %>% clean_names())
## [1] "no_overall"           "no_inseason"          "title"               
## [4] "directed_by"          "written_by"           "original_air_date"   
## [7] "prod_code_40"         "u_s_viewers_millions"
```


