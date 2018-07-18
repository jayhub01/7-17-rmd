# 7-17-rmd

---
title: ""
output: html_document
---

# Load your libraries here

```{r}
library(readxl)
library(dplyr)
library(tidyverse)
library(ggplot2)
```

First get the url for the file you want, and download it, ideally to a folder. I created a project called immigration and a folder called downloads. I got the URL from the website and noted that it was a zip file. (Commenting out temporarily since I don't want to overwrite my data)

```{r}
download.file("https://www.dhs.gov/sites/default/files/publications/LPR2006_0.zip",
             destfile = "downloads/lpr2006.zip")
download.file("https://www.dhs.gov/sites/default/files/publications/YRBK%202016%20LPR%20Excel%20Final_0.zip",
             destfile = "downloads/lpr2016.zip")
```


Get a list of the files in the zip, which allows you to see the file name to insert below in the unzip command.

```{r}
unzip("downloads/lpr2006.zip", list = TRUE)
unzip("downloads/lpr2016.zip", list = TRUE)
```

Unzipped Table 6 for both datasets.  First dataset is from 1997 to 2006; Second dataset is from 2007 to 2016.

```{r}
unzip("downloads/lpr2006.zip", exdir = "downloads/unzipped", files = "Table 06D.xls")
unzip("downloads/lpr2016.zip", exdir = "downloads/unzipped", files = "YRBK 2016 LPR Excel Final/fy2016_table6.xls")

```

So now table6 is at "downloads2/unzipped/Table 06D.xls" and downloads2/unzipped/YRBK 2016 LPR Excel Final/fy2016_table6.xls. Created two variables for both unzipped files, path1 and path2.

```{r}
path1 <- "~/downloads/unzipped/Table 06D.xls"
path2 <- "~/downloads/unzipped/YRBK 2016 LPR Excel Final/fy2016_table6.xls"

```

First, I'm reading in the excel file(.xls) that contains files from 1997 to 2006 using the variable name, path1. Then, read in the 2nd, excel file containing records from 2007 to 2016.

Skipped the first. three rows and converted all "D" strings to 'NA'

Tip: Reading the help file for readxl (?read_excel)

```{r}
tableSix_1997to2006 <- read_excel(path1, na ="D", skip = 3)
tableSix_2007to2016 <- read_excel(path2, na ="D", skip = 3)

```

Merging two data tables together; 1st dataset is from 1997 to 2006 and 2nd dataset is from 2007 to 2016; however, still have multiple duplicate rows even though I have 'no.dups=TRUE' below.


```{r}
myfulldata_1 <- merge(x = tableSix_1997to2006 , y = tableSix_2007to2016, sort = F, no.dups = T)

```

Last step eliminated all duplicate rows from dataset one, 1997 to 2006 by creating the 'filtered_df' and then, deleted the duplicate rows from the 2nd dataset, 2007 to 2016 by creating 'filtered_df2'.

```{r}
filtered_df <- myfulldata_1[!duplicated(myfulldata_1$`1997`),]
filtered_df2 <- filtered_df[!duplicated(filtered_df$`2007`) ,]

#changing all the -s dashes in the dataset to 0
filtered_df2$`2007`[filtered_df2$`2007`=="-"] <- "0"
filtered_df2$`2008`[filtered_df2$`2008`=="-"] <- "0"
filtered_df2$`2009`[filtered_df2$`2009`=="-"] <- "0"
filtered_df2$`2010`[filtered_df2$`2010`=="-"] <- "0"
filtered_df2$`2011`[filtered_df2$`2011`=="-"] <- "0"
filtered_df2$`2012`[filtered_df2$`2012`=="-"] <- "0"
filtered_df2$`2013`[filtered_df2$`2013`=="-"] <- "0"
filtered_df2$`2014`[filtered_df2$`2014`=="-"] <- "0"
filtered_df2$`2015`[filtered_df2$`2015`=="-"] <- "0"
filtered_df2$`2016`[filtered_df2$`2016`=="-"] <- "0"
 
#changing the class of all the observations for all years to "numeric" from "character"

ix <- 2:21
filtered_df2[ix] <- lapply(filtered_df2[ix], as.numeric)

