# Задание 1: Найти утечку данных из сети.
Один из хостов в нашей сети используется для пересылки этой информации – он пересылает гораздо больше информации на внешние ресурсы в Интернете, чем остальные компьютеры нашей сети. Определите его IP-адрес.

``` r
library(stringr)
library(dplyr)
library(tidymodels)
library(arrow)
```

``` r
df_data <- arrow::read_csv_arrow("traffic_security.csv")
colnames(df_data) <- c('timestamp','src','dst','port','bytes')
head(df_data,3)
```

``` r
knitr::opts_chunk$set(
  df_data <- df_data[df_data$src > 11 & df_data$src < 15 & df_data$dst < 11 | df_data$dst > 15, ]
)

knitr::opts_chunk$set(
 found_ip1 <- df_data %>%
            group_by(src) %>%
            summarise(bytes = mean(bytes)),
  found_ip1 <- found_ip1[which.max(found_ip1$bytes),],
  print(found_ip1) 
)
```
Ответ: 13.37.84.125
