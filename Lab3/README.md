---
title: "Lab3"
author: "KovalevP.A."
date: '2023-05-24'
output: html_document
---

# Задание 3

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(stringr)
library(arrow)
library(dplyr)
library(cluster)
```

## Импортируем данные
```{r}
df <- arrow::read_csv_arrow("traffic_security.csv")
```

```{r}
sel<-filter(df, str_detect(src,"^((12|13|14)\\.)"),str_detect(dst,"^((12|13|14)\\.)",negate=TRUE))%>%
  select(port,bytes,src)
sam<-sel[order(sel$port, decreasing = TRUE), ]%>%group_by(src,port)
sam2<-sam%>% summarize(b_src_port = sum(bytes))%>%group_by(port)
sam3<-sam2 %>% summarize(b_port = mean(b_src_port))
sam4<-merge(sam, sam3, by = "port")
sam4$diff <- sam4$bytes - sam4$b_port
sam4[order(sam4$diff, decreasing = TRUE), ]
res<-filter(sam4,str_detect(src,"13.37.84.125",negate=TRUE)&str_detect(src,"13.48.72.30",negate=TRUE)) %>% head(1)
res %>% select(src)
```