---
title: "Raport - life expectancy"
author: "Jan Hałasiński"
output: 
  html_document:
    toc: true
    toc_float: true
    keep_md: true
---



# Projekt z przedmiotu: Eksploracja masywnych danych
date: "2020-12-18"

#### Wykorzystane pakiety:

```r
library(knitr)
library(pastecs)
library(reshape2)
library(tidyr)
library(dplyr)
library(ggplot2)
library(corrplot)
library(caret)
library(sgd)
```

## Podsumowanie Analizy

Na podstawie obserwacji oraz wykonanych analiz nasuwają się wnioski, że oczekiwana długość życia zależy w największym stopniu od bogactwa społeczeństwa. Najbardziej znaczącym parametrem okazała się śmiertelność dorosłych. Ponieważ jeśli długość życia zależy W dużej mierze od przeżywalności dorosłych, to znaczy, że śmiertelność wśród dzieci ma tu mały wpływ. Potwierdzają to uzyskane wyniki. Ponad to widać pozytywną zależność od bogactwa społeczeństwa i edukacji. Ma to sens, ponieważ kraje bogate stać na edukowanie lekarzy, a brak problemów z żywieniem pozwala młodzieży na kształcenie. Negatywna zależność występuje najmocniej dla zachorowań na AIDS i niedożywienia wśród dzieci i młodzieży. Choroby typu polio i zapalenie wątroby typu B lub błonica mają mniejszy wpływ. Co ciekawe spożycie alkoholu w różnych krajach jest dodatnio skorelowane z przewidywaną długością życia. Rozsądek podpowiada, że spożywanie trucizn raczej przeciwdziała przeżywalności. Można to tłumaczyć w ten sposób, że jest również skorelowane ze wskaźnikami bogactwa społeczeństwa oraz przyczynami religijnymi i kulturowymi w biedniejszych krajach. 

## Dane
Wczytanie i podgląd danych

```r
data <- read.csv("Life_Expectancy_Data.csv")
raw.data <- data
```

|Country     | Year|Status     | Life.expectancy| Adult.Mortality| infant.deaths| Alcohol| percentage.expenditure|
|:-----------|----:|:----------|---------------:|---------------:|-------------:|-------:|----------------------:|
|Afghanistan | 2015|Developing |            65.0|             263|            62|    0.01|                  71.28|
|Afghanistan | 2014|Developing |            59.9|             271|            64|    0.01|                  73.52|
|Afghanistan | 2013|Developing |            59.9|             268|            66|    0.01|                  73.22|
|Afghanistan | 2012|Developing |            59.5|             272|            69|    0.01|                  78.18|
|Afghanistan | 2011|Developing |            59.2|             275|            71|    0.01|                   7.10|
|Afghanistan | 2010|Developing |            58.8|             279|            74|    0.01|                  79.68|



| Hepatitis.B| Measles|  BMI| under.five.deaths| Polio| Total.expenditure| Diphtheria| HIV.AIDS|    GDP|
|-----------:|-------:|----:|-----------------:|-----:|-----------------:|----------:|--------:|------:|
|          65|    1154| 19.1|                83|     6|              8.16|         65|      0.1| 584.26|
|          62|     492| 18.6|                86|    58|              8.18|         62|      0.1| 612.70|
|          64|     430| 18.1|                89|    62|              8.13|         64|      0.1| 631.74|
|          67|    2787| 17.6|                93|    67|              8.52|         67|      0.1| 669.96|
|          68|    3013| 17.2|                97|    68|              7.87|         68|      0.1|  63.54|
|          66|    1989| 16.7|               102|    66|              9.20|         66|      0.1| 553.33|



| Population| thinness..1.19.years| thinness.5.9.years| Income.composition.of.resources| Schooling|
|----------:|--------------------:|------------------:|-------------------------------:|---------:|
|   33736494|                 17.2|               17.3|                            0.48|      10.1|
|     327582|                 17.5|               17.5|                            0.48|      10.0|
|   31731688|                 17.7|               17.7|                            0.47|       9.9|
|    3696958|                 17.9|               18.0|                            0.46|       9.8|
|    2978599|                 18.2|               18.2|                            0.45|       9.5|
|    2883167|                 18.4|               18.4|                            0.45|       9.2|

Strutura zestawu danych

```r
str(data)
```

```
## 'data.frame':	2938 obs. of  22 variables:
##  $ Country                        : Factor w/ 193 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ Year                           : int  2015 2014 2013 2012 2011 2010 2009 2008 2007 2006 ...
##  $ Status                         : Factor w/ 2 levels "Developed","Developing": 2 2 2 2 2 2 2 2 2 2 ...
##  $ Life.expectancy                : num  65 59.9 59.9 59.5 59.2 58.8 58.6 58.1 57.5 57.3 ...
##  $ Adult.Mortality                : int  263 271 268 272 275 279 281 287 295 295 ...
##  $ infant.deaths                  : int  62 64 66 69 71 74 77 80 82 84 ...
##  $ Alcohol                        : num  0.01 0.01 0.01 0.01 0.01 0.01 0.01 0.03 0.02 0.03 ...
##  $ percentage.expenditure         : num  71.3 73.5 73.2 78.2 7.1 ...
##  $ Hepatitis.B                    : int  65 62 64 67 68 66 63 64 63 64 ...
##  $ Measles                        : int  1154 492 430 2787 3013 1989 2861 1599 1141 1990 ...
##  $ BMI                            : num  19.1 18.6 18.1 17.6 17.2 16.7 16.2 15.7 15.2 14.7 ...
##  $ under.five.deaths              : int  83 86 89 93 97 102 106 110 113 116 ...
##  $ Polio                          : int  6 58 62 67 68 66 63 64 63 58 ...
##  $ Total.expenditure              : num  8.16 8.18 8.13 8.52 7.87 9.2 9.42 8.33 6.73 7.43 ...
##  $ Diphtheria                     : int  65 62 64 67 68 66 63 64 63 58 ...
##  $ HIV.AIDS                       : num  0.1 0.1 0.1 0.1 0.1 0.1 0.1 0.1 0.1 0.1 ...
##  $ GDP                            : num  584.3 612.7 631.7 670 63.5 ...
##  $ Population                     : num  33736494 327582 31731688 3696958 2978599 ...
##  $ thinness..1.19.years           : num  17.2 17.5 17.7 17.9 18.2 18.4 18.6 18.8 19 19.2 ...
##  $ thinness.5.9.years             : num  17.3 17.5 17.7 18 18.2 18.4 18.7 18.9 19.1 19.3 ...
##  $ Income.composition.of.resources: num  0.479 0.476 0.47 0.463 0.454 0.448 0.434 0.433 0.415 0.405 ...
##  $ Schooling                      : num  10.1 10 9.9 9.8 9.5 9.2 8.9 8.7 8.4 8.1 ...
```

Przegląd podstawowych cech danych względem atrybutów:

```r
descr <- stat.desc(data, p=0.95)
```

|         |Country |       Year|Status | Life.expectancy| Adult.Mortality| infant.deaths|  Alcohol| percentage.expenditure|
|:--------|:-------|----------:|:------|---------------:|---------------:|-------------:|--------:|----------------------:|
|nbr.val  |NA      |    2938.00|NA     |         2928.00|         2928.00|       2938.00|  2744.00|                2938.00|
|nbr.null |NA      |       0.00|NA     |            0.00|            0.00|        848.00|     0.00|                 611.00|
|nbr.na   |NA      |       0.00|NA     |           10.00|           10.00|          0.00|   194.00|                   0.00|
|min      |NA      |    2000.00|NA     |           36.30|            1.00|          0.00|     0.01|                   0.00|
|max      |NA      |    2015.00|NA     |           89.00|          723.00|       1800.00|    17.87|               19479.91|
|range    |NA      |      15.00|NA     |           52.70|          722.00|       1800.00|    17.86|               19479.91|
|sum      |NA      | 5898090.00|NA     |       202690.60|       482524.00|      89033.00| 12630.25|             2168982.31|
|median   |NA      |    2008.00|NA     |           72.10|          144.00|          3.00|     3.75|                  64.91|
|mean     |NA      |    2007.52|NA     |           69.22|          164.80|         30.30|     4.60|                 738.25|
|SE.mean  |NA      |       0.09|NA     |            0.18|            2.30|          2.18|     0.08|                  36.68|
|CI.mean  |NA      |       0.17|NA     |            0.35|            4.50|          4.27|     0.15|                  71.91|
|var      |NA      |      21.29|NA     |           90.70|        15448.52|      13906.66|    16.42|             3951805.48|
|std.dev  |NA      |       4.61|NA     |            9.52|          124.29|        117.93|     4.05|                1987.91|
|coef.var |NA      |       0.00|NA     |            0.14|            0.75|          3.89|     0.88|                   2.69|



|         | Hepatitis.B|      Measles|       BMI| under.five.deaths|     Polio| Total.expenditure| Diphtheria| HIV.AIDS|          GDP|
|:--------|-----------:|------------:|---------:|-----------------:|---------:|-----------------:|----------:|--------:|------------:|
|nbr.val  |     2385.00|      2938.00|   2904.00|           2938.00|   2919.00|           2712.00|    2919.00|  2938.00|      2490.00|
|nbr.null |        0.00|       983.00|      0.00|            785.00|      0.00|              0.00|       0.00|     0.00|         0.00|
|nbr.na   |      553.00|         0.00|     34.00|              0.00|     19.00|            226.00|      19.00|     0.00|       448.00|
|min      |        1.00|         0.00|      1.00|              0.00|      3.00|              0.37|       2.00|     0.10|         1.68|
|max      |       99.00|    212183.00|     87.30|           2500.00|     99.00|             17.60|      99.00|    50.60|    119172.74|
|range    |       98.00|    212183.00|     86.30|           2500.00|     96.00|             17.23|      97.00|    50.50|    119171.06|
|sum      |   193043.00|   7108762.00| 111284.90|         123501.00| 240964.00|          16104.37|  240304.00|  5118.30|  18633064.59|
|median   |       92.00|        17.00|     43.50|              4.00|     93.00|              5.75|      93.00|     0.10|      1766.95|
|mean     |       80.94|      2419.59|     38.32|             42.04|     82.55|              5.94|      82.32|     1.74|      7483.16|
|SE.mean  |        0.51|       211.56|      0.37|              2.96|      0.43|              0.05|       0.44|     0.09|       285.98|
|CI.mean  |        1.01|       414.82|      0.73|              5.80|      0.85|              0.09|       0.86|     0.18|       560.78|
|var      |      628.51| 131498338.34|    401.76|          25742.77|    548.87|              6.24|     562.49|    25.78| 203637733.04|
|std.dev  |       25.07|     11467.27|     20.04|            160.45|     23.43|              2.50|      23.72|     5.08|     14270.17|
|coef.var |        0.31|         4.74|      0.52|              3.82|      0.28|              0.42|       0.29|     2.91|         1.91|



|         |   Population| thinness..1.19.years| thinness.5.9.years| Income.composition.of.resources| Schooling|
|:--------|------------:|--------------------:|------------------:|-------------------------------:|---------:|
|nbr.val  | 2.286000e+03|              2904.00|            2904.00|                         2771.00|   2775.00|
|nbr.null | 0.000000e+00|                 0.00|               0.00|                          130.00|     28.00|
|nbr.na   | 6.520000e+02|                34.00|              34.00|                          167.00|    163.00|
|min      | 3.400000e+01|                 0.10|               0.10|                            0.00|      0.00|
|max      | 1.293859e+09|                27.70|              28.60|                            0.95|     20.70|
|range    | 1.293859e+09|                27.60|              28.50|                            0.95|     20.70|
|sum      | 2.915422e+10|             14054.50|           14143.40|                         1738.94|  33280.00|
|median   | 1.386542e+06|                 3.30|               3.30|                            0.68|     12.30|
|mean     | 1.275338e+07|                 4.84|               4.87|                            0.63|     11.99|
|SE.mean  | 1.276080e+06|                 0.08|               0.08|                            0.00|      0.06|
|CI.mean  | 2.502396e+06|                 0.16|               0.16|                            0.01|      0.13|
|var      | 3.722476e+15|                19.54|              20.33|                            0.04|     11.28|
|std.dev  | 6.101210e+07|                 4.42|               4.51|                            0.21|      3.36|
|coef.var | 4.780000e+00|                 0.91|               0.93|                            0.34|      0.28|

W zbiorze występują zmiene tekstowe: Nazwa kraju oraz status. Drugi z nich zawiera tylko dwie wartości, więc można te wartości enumeruować w do analizy. 

```r
data$Status <- sapply(data$Status == "Developed", as.numeric)
```

Udział wierszy z NA w zbiorze jest wysoki. 

```r
nrow( data[complete.cases(data), ]) / nrow(data)
```

```
## [1] 0.5612662
```

W związku z tym zbadano, dla jakich kolumn najczęściej występują wybrakowane dane oraz porównano je z korelacją między atrybutami.


```r
corrupted_data <- data %>% 
  summarise_all(list(~sum(is.na(.))/length(.))) %>%
  gather(key="column", value="corrupted") %>%
  filter(corrupted > 0) %>%
  ggplot(aes(x=reorder(column, corrupted), y=corrupted)) +
  geom_bar(stat="identity") +
  coord_flip()

corrupted_data
```

![](Raport_files/figure-html/missing_plot-1.png)<!-- -->


```r
corr.data <- data[complete.cases(data),]
numerics <- attributes(corr.data[unlist(lapply(corr.data, is.numeric))])$names
cor_mat <- cor(corr.data[numerics])

corrplot(cor_mat, type = "upper", order = "hclust", 
         tl.col = "black", tl.srt=45, tl.cex=10/ncol(cor_mat))
```

![](Raport_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
Na podstawie powyższych wykresów widać, że najczęściej brakuje danych dotyczących populacji kraju. Ponieważ ta kolumna jest słabo skorelowana z pozostałymi kolumnami (śmiertelność wśród dzieci i noworodków jest wyrażona bezwzględnie, co tłumaczy zwiększoną korelację). Populacja nie będzie zatem brana pod uwagę podczas dalszej analizy. Kolumny "thinnes..." wykazują również bardzo wysoką korelację, bliską jedynki. Kolumna "thinnes.5.9.years" zostanie również usunięta jako zbędna. Kolumny "Percentage Expenditure" i "GDP" również są skorelowane, a danych na temat PKB brakuje w ponad 15% wierszy 


```r
data <- data[ , !(names(data) %in% c("Population", "thinness.5.9.years", "GDP") )]
nrow( data[complete.cases(data), ]) / nrow(data)
```

```
## [1] 0.7089857
```

Udział kompletnych wierszy w zbiorze wzrósł z ok. 56,1 % do 70,8%. 
Dalszej analizie będą podlegać pozostałe kompletne przypadki.


```r
data <- data[complete.cases(data),]
```

## wykresy rozkładów wartości atrybutów

```r
d <- melt(data[,-c(1:3)])
```

```
## No id variables; using all as measure variables
```

```r
ggplot(d,aes(x = value)) + 
    facet_wrap(~variable, scales = "free", ncol=3, nrow=10) + 
    geom_histogram(bins=50)
```

![](Raport_files/figure-html/distributions-1.png)<!-- -->

Mozna wyróżnić atrybuty o rozkladzie bliskim rozkładowi Pareta, np.: Percentage expenditure, Measles, Infant deaths. Dla innej grupy atrybutów, wartości przypominają przesunięty rozkład normalny. Najbardziej nieregularny jest BMI.

## Oczekiwana długość życia w poszczególnych krajach
( active mode only )

## Regresor przewidujący długość życia

```r
reg <- data[unlist(lapply(data, is.numeric))]
preproc <- preProcess(reg, method=c("range"))
reg.data <- predict(preproc, reg)

test.set <- sample(1:nrow(reg.data), size=nrow(reg.data)/8, replace=FALSE)
reg.data.test <- reg.data[ test.set, ]
reg.data.train <- reg.data[ -test.set, ]

sgd.train <- sgd(Life.expectancy ~ . , data=reg.data.train,
               model="glm", model.control=binomial(link="logit"),
               sgd.control=list(reltol=1e-5, npasses=200),
                 lr.control=c(scale=1, gamma=1, alpha=30, c=1))

sgd.test <- sgd(Life.expectancy ~ . , data=reg.data.test,
               model="glm", model.control=binomial(link="logit"),
               sgd.control=list(reltol=1e-5, npasses=200),
                 lr.control=c(scale=1, gamma=1, alpha=30, c=1))

r2.train <- summary(lm( sgd.train$fitted.values ~ reg.data.train[,3]))$r.squared
r2.test <- summary(lm( sgd.test$fitted.values ~ reg.data.test[,3]))$r.squared

M <- cbind(names(reg.data), sgd.test$coefficients)
M[order(as.numeric(M[,2]), decreasing = TRUE), ]
```

```
##       [,1]                              [,2]                 
##  [1,] "Income.composition.of.resources" "0.514943114809324"  
##  [2,] "BMI"                             "0.501429124062584"  
##  [3,] "Life.expectancy"                 "0.441035327564526"  
##  [4,] "Schooling"                       "0.300088357293019"  
##  [5,] "Alcohol"                         "0.260142792905993"  
##  [6,] "percentage.expenditure"          "0.201477994718097"  
##  [7,] "Polio"                           "0.0720575708015166" 
##  [8,] "Diphtheria"                      "0.0465393802098883" 
##  [9,] "Total.expenditure"               "0.0423419326460256" 
## [10,] "Measles"                         "-0.0240626967676143"
## [11,] "infant.deaths"                   "-0.0732999359196081"
## [12,] "Status"                          "-0.0850644724019506"
## [13,] "under.five.deaths"               "-0.0887464183402903"
## [14,] "Hepatitis.B"                     "-0.0948872925585826"
## [15,] "Year"                            "-0.327112506817078" 
## [16,] "thinness..1.19.years"            "-0.352195507485349" 
## [17,] "HIV.AIDS"                        "-0.492009501018055" 
## [18,] "Adult.Mortality"                 "-0.717529630792094"
```


```
## [1] "R^2 na zbiorze uczącym: 69.384 %"
```

```
## [1] "R^2 na zbiorze testowym: 72.655 %"
```

```
## [1] "RMSE na zbiorze uczącym: 11.264 %"
```

```
## [1] "RMSE na zbiorze testowym: 11.053 %"
```




