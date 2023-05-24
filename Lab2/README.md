# Задание 2: Поиск утечки данных 2
Другой атакующий установил автоматическую задачу в системном планировщике cron для экспорта содержимого внутренней wiki системы. Эта система генерирует большое количество траффика в нерабочие часы, больше чем остальные хосты. Определите IP этой системы. Известно, что ее IP адрес отличается от нарушителя из предыдущей задачи.

```{r}
library(stringr)
library(dplyr)
library(tidymodels)
library(arrow)
```

```{r}
df <- arrow::read_csv_arrow("traffic_security.csv")
colnames(df) <- c('ts','src','dst','port','bytes')
```

```{r}
df$hour <- with (df,format(as.POSIXct(df$ts), format = "%H"))
df$minutes <- with (df,format(as.POSIXct(df$ts), format = "%M"))
```

#Рабочие часы
```{r}
activhours <- df %>% group_by(hour) %>% summarise(N = n())
select(arrange(activhours,desc(N)),N,hour)
```
Ответ: 16-23

```{r}
res <- df %>% 
  filter(src != "13.37.84.125") %>% #не адрес из 1 пункта
  filter(src_info == TRUE) %>% #исходящий трафик
  filter(dst_info == FALSE) %>%
  filter(hour >= 0) %>% #нерабочие часы
  filter(hour < 16) %>%
  group_by(src) %>%
  summarise(bytes = mean(bytes))
select(arrange(res,desc(bytes)) %>% top_n(1),src) 
```

Ответ: 12.55.77.96
