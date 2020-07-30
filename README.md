# Tantalus

An R package for exploration of [Serratus](https://github.com/ababaian/serratus) data.

![Tantalus Mountain in Squamish, BC. Canada](img/tantalus.png)

## Installation
To install directly from the repo:

```
install.packages("devtools")
library(devtools)

install_github("serratus-bio/tantalus")
```

Note, if you install tantalus with R version <3.6.0, then you first need to install older version of pbkrtest:

```
packageurl <- "http://cran.r-project.org/src/contrib/Archive/pbkrtest/pbkrtest_0.4-7.tar.gz"
install.packages(packageurl, repos=NULL, type="source")
```

## Usage example

To load data with **tantalus** you should import RPostgresSQL and dbplyr packages:

```R
library("tantalus")
library("dbplyr")
library("RPostgreSQL") 

drv <- DBI::dbDriver("PostgreSQL")
con <- DBI::dbConnect(drv, 
                      user="postgres", 
                      password="serratus",
                      host="big-parse-db.ccz9y6yshbls.us-east-1.rds.amazonaws.com", 
                      port=5432, 
                      dbname="postgres")
#with a database
##tbls: __EFMigrationsHistory, AccessionSections, FamilySections, FastaSections, Runs

#get Family table, and Coronaviridae family (and get just specific columns)
x <- readDfSQL(con, "FamilySections", family = "Coronaviridae", dataframe = T, 
               columns = c("Sra","Score", "PctId", "Family"))
#get specific SRAs
sras <- c("SRR10144611", "SRR6906297", "SRR6906298",  "SRR6906299", 
          "SRR6906300", "SRR6906303", "SRR3229029", "SRR3229077", 
          "SRR3229078", "SRR3229081")

x_specific <- readDfSQL(con, "FamilySections", family = "Coronaviridae", sras = sras, dataframe = T, 
                        columns = c("Sra","Score", "PctId", "Family"))
```

Plot examples:

```R
colnames(x) <- tolower(colnames(x))

#plot pctid histogram with family score intervals
p <- pctidInScoreIntervals(x, family_name = "Coronaviridae", title="Coronaviridae",
                      scale_log = F, bin_scores = F)
p
```

![](img/new_img_1.png)

```R
#plot pctid histogram with family score intervals
p <- scoreInPctidIntervals(x, family_name = "Coronaviridae", title="Coronaviridae",
                      scale_log = F, bin_percent = T)
p
```

![](img/new_img_2.png)