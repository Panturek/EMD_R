Projekt z przedmiotu: Eksploracja masywnych danych
==================================================

date: “2020-12-18”

#### Wykorzystane pakiety:

    library(knitr)
    library(pastecs)
    library(reshape2)
    library(tidyr)
    library(dplyr)
    library(ggplot2)
    library(corrplot)
    library(caret)
    library(sgd)

Podsumowanie Analizy
--------------------

Na podstawie obserwacji oraz wykonanych analiz nasuwają się wnioski, że
oczekiwana długość życia zależy w największym stopniu od bogactwa
społeczeństwa. Najbardziej znaczącym parametrem okazała się śmiertelność
dorosłych. Ponieważ jeśli długość życia zależy W dużej mierze od
przeżywalności dorosłych, to znaczy, że śmiertelność wśród dzieci ma tu
mały wpływ. Potwierdzają to uzyskane wyniki. Ponad to widać pozytywną
zależność od bogactwa społeczeństwa i edukacji. Ma to sens, ponieważ
kraje bogate stać na edukowanie lekarzy, a brak problemów z żywieniem
pozwala młodzieży na kształcenie. Negatywna zależność występuje
najmocniej dla zachorowań na AIDS i niedożywienia wśród dzieci i
młodzieży. Choroby typu polio i zapalenie wątroby typu B lub błonica
mają mniejszy wpływ. Co ciekawe spożycie alkoholu w różnych krajach jest
dodatnio skorelowane z przewidywaną długością życia. Rozsądek
podpowiada, że spożywanie trucizn raczej przeciwdziała przeżywalności.
Można to tłumaczyć w ten sposób, że jest również skorelowane ze
wskaźnikami bogactwa społeczeństwa oraz przyczynami religijnymi i
kulturowymi w biedniejszych krajach.

Dane
----

Wczytanie i podgląd danych

    data <- read.csv("Life_Expectancy_Data.csv")
    raw.data <- data

<table style="width:100%;">
<colgroup>
<col style="width: 11%" />
<col style="width: 4%" />
<col style="width: 10%" />
<col style="width: 15%" />
<col style="width: 15%" />
<col style="width: 13%" />
<col style="width: 7%" />
<col style="width: 21%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">Country</th>
<th style="text-align: right;">Year</th>
<th style="text-align: left;">Status</th>
<th style="text-align: right;">Life.expectancy</th>
<th style="text-align: right;">Adult.Mortality</th>
<th style="text-align: right;">infant.deaths</th>
<th style="text-align: right;">Alcohol</th>
<th style="text-align: right;">percentage.expenditure</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Afghanistan</td>
<td style="text-align: right;">2015</td>
<td style="text-align: left;">Developing</td>
<td style="text-align: right;">65.0</td>
<td style="text-align: right;">263</td>
<td style="text-align: right;">62</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">71.28</td>
</tr>
<tr class="even">
<td style="text-align: left;">Afghanistan</td>
<td style="text-align: right;">2014</td>
<td style="text-align: left;">Developing</td>
<td style="text-align: right;">59.9</td>
<td style="text-align: right;">271</td>
<td style="text-align: right;">64</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">73.52</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Afghanistan</td>
<td style="text-align: right;">2013</td>
<td style="text-align: left;">Developing</td>
<td style="text-align: right;">59.9</td>
<td style="text-align: right;">268</td>
<td style="text-align: right;">66</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">73.22</td>
</tr>
<tr class="even">
<td style="text-align: left;">Afghanistan</td>
<td style="text-align: right;">2012</td>
<td style="text-align: left;">Developing</td>
<td style="text-align: right;">59.5</td>
<td style="text-align: right;">272</td>
<td style="text-align: right;">69</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">78.18</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Afghanistan</td>
<td style="text-align: right;">2011</td>
<td style="text-align: left;">Developing</td>
<td style="text-align: right;">59.2</td>
<td style="text-align: right;">275</td>
<td style="text-align: right;">71</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">7.10</td>
</tr>
<tr class="even">
<td style="text-align: left;">Afghanistan</td>
<td style="text-align: right;">2010</td>
<td style="text-align: left;">Developing</td>
<td style="text-align: right;">58.8</td>
<td style="text-align: right;">279</td>
<td style="text-align: right;">74</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">79.68</td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col style="width: 12%" />
<col style="width: 8%" />
<col style="width: 5%" />
<col style="width: 19%" />
<col style="width: 6%" />
<col style="width: 19%" />
<col style="width: 11%" />
<col style="width: 9%" />
<col style="width: 7%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: right;">Hepatitis.B</th>
<th style="text-align: right;">Measles</th>
<th style="text-align: right;">BMI</th>
<th style="text-align: right;">under.five.deaths</th>
<th style="text-align: right;">Polio</th>
<th style="text-align: right;">Total.expenditure</th>
<th style="text-align: right;">Diphtheria</th>
<th style="text-align: right;">HIV.AIDS</th>
<th style="text-align: right;">GDP</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: right;">65</td>
<td style="text-align: right;">1154</td>
<td style="text-align: right;">19.1</td>
<td style="text-align: right;">83</td>
<td style="text-align: right;">6</td>
<td style="text-align: right;">8.16</td>
<td style="text-align: right;">65</td>
<td style="text-align: right;">0.1</td>
<td style="text-align: right;">584.26</td>
</tr>
<tr class="even">
<td style="text-align: right;">62</td>
<td style="text-align: right;">492</td>
<td style="text-align: right;">18.6</td>
<td style="text-align: right;">86</td>
<td style="text-align: right;">58</td>
<td style="text-align: right;">8.18</td>
<td style="text-align: right;">62</td>
<td style="text-align: right;">0.1</td>
<td style="text-align: right;">612.70</td>
</tr>
<tr class="odd">
<td style="text-align: right;">64</td>
<td style="text-align: right;">430</td>
<td style="text-align: right;">18.1</td>
<td style="text-align: right;">89</td>
<td style="text-align: right;">62</td>
<td style="text-align: right;">8.13</td>
<td style="text-align: right;">64</td>
<td style="text-align: right;">0.1</td>
<td style="text-align: right;">631.74</td>
</tr>
<tr class="even">
<td style="text-align: right;">67</td>
<td style="text-align: right;">2787</td>
<td style="text-align: right;">17.6</td>
<td style="text-align: right;">93</td>
<td style="text-align: right;">67</td>
<td style="text-align: right;">8.52</td>
<td style="text-align: right;">67</td>
<td style="text-align: right;">0.1</td>
<td style="text-align: right;">669.96</td>
</tr>
<tr class="odd">
<td style="text-align: right;">68</td>
<td style="text-align: right;">3013</td>
<td style="text-align: right;">17.2</td>
<td style="text-align: right;">97</td>
<td style="text-align: right;">68</td>
<td style="text-align: right;">7.87</td>
<td style="text-align: right;">68</td>
<td style="text-align: right;">0.1</td>
<td style="text-align: right;">63.54</td>
</tr>
<tr class="even">
<td style="text-align: right;">66</td>
<td style="text-align: right;">1989</td>
<td style="text-align: right;">16.7</td>
<td style="text-align: right;">102</td>
<td style="text-align: right;">66</td>
<td style="text-align: right;">9.20</td>
<td style="text-align: right;">66</td>
<td style="text-align: right;">0.1</td>
<td style="text-align: right;">553.33</td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col style="width: 11%" />
<col style="width: 22%" />
<col style="width: 20%" />
<col style="width: 34%" />
<col style="width: 10%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: right;">Population</th>
<th style="text-align: right;">thinness..1.19.years</th>
<th style="text-align: right;">thinness.5.9.years</th>
<th style="text-align: right;">Income.composition.of.resources</th>
<th style="text-align: right;">Schooling</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: right;">33736494</td>
<td style="text-align: right;">17.2</td>
<td style="text-align: right;">17.3</td>
<td style="text-align: right;">0.48</td>
<td style="text-align: right;">10.1</td>
</tr>
<tr class="even">
<td style="text-align: right;">327582</td>
<td style="text-align: right;">17.5</td>
<td style="text-align: right;">17.5</td>
<td style="text-align: right;">0.48</td>
<td style="text-align: right;">10.0</td>
</tr>
<tr class="odd">
<td style="text-align: right;">31731688</td>
<td style="text-align: right;">17.7</td>
<td style="text-align: right;">17.7</td>
<td style="text-align: right;">0.47</td>
<td style="text-align: right;">9.9</td>
</tr>
<tr class="even">
<td style="text-align: right;">3696958</td>
<td style="text-align: right;">17.9</td>
<td style="text-align: right;">18.0</td>
<td style="text-align: right;">0.46</td>
<td style="text-align: right;">9.8</td>
</tr>
<tr class="odd">
<td style="text-align: right;">2978599</td>
<td style="text-align: right;">18.2</td>
<td style="text-align: right;">18.2</td>
<td style="text-align: right;">0.45</td>
<td style="text-align: right;">9.5</td>
</tr>
<tr class="even">
<td style="text-align: right;">2883167</td>
<td style="text-align: right;">18.4</td>
<td style="text-align: right;">18.4</td>
<td style="text-align: right;">0.45</td>
<td style="text-align: right;">9.2</td>
</tr>
</tbody>
</table>

Strutura zestawu danych

    str(data)

    ## 'data.frame':    2938 obs. of  22 variables:
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

Przegląd podstawowych cech danych względem atrybutów:

    descr <- stat.desc(data, p=0.95)

<table>
<colgroup>
<col style="width: 7%" />
<col style="width: 7%" />
<col style="width: 9%" />
<col style="width: 6%" />
<col style="width: 14%" />
<col style="width: 14%" />
<col style="width: 12%" />
<col style="width: 7%" />
<col style="width: 20%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;"></th>
<th style="text-align: left;">Country</th>
<th style="text-align: right;">Year</th>
<th style="text-align: left;">Status</th>
<th style="text-align: right;">Life.expectancy</th>
<th style="text-align: right;">Adult.Mortality</th>
<th style="text-align: right;">infant.deaths</th>
<th style="text-align: right;">Alcohol</th>
<th style="text-align: right;">percentage.expenditure</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">nbr.val</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">2938.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">2928.00</td>
<td style="text-align: right;">2928.00</td>
<td style="text-align: right;">2938.00</td>
<td style="text-align: right;">2744.00</td>
<td style="text-align: right;">2938.00</td>
</tr>
<tr class="even">
<td style="text-align: left;">nbr.null</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">848.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">611.00</td>
</tr>
<tr class="odd">
<td style="text-align: left;">nbr.na</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">10.00</td>
<td style="text-align: right;">10.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">194.00</td>
<td style="text-align: right;">0.00</td>
</tr>
<tr class="even">
<td style="text-align: left;">min</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">2000.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">36.30</td>
<td style="text-align: right;">1.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.00</td>
</tr>
<tr class="odd">
<td style="text-align: left;">max</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">2015.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">89.00</td>
<td style="text-align: right;">723.00</td>
<td style="text-align: right;">1800.00</td>
<td style="text-align: right;">17.87</td>
<td style="text-align: right;">19479.91</td>
</tr>
<tr class="even">
<td style="text-align: left;">range</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">15.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">52.70</td>
<td style="text-align: right;">722.00</td>
<td style="text-align: right;">1800.00</td>
<td style="text-align: right;">17.86</td>
<td style="text-align: right;">19479.91</td>
</tr>
<tr class="odd">
<td style="text-align: left;">sum</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">5898090.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">202690.60</td>
<td style="text-align: right;">482524.00</td>
<td style="text-align: right;">89033.00</td>
<td style="text-align: right;">12630.25</td>
<td style="text-align: right;">2168982.31</td>
</tr>
<tr class="even">
<td style="text-align: left;">median</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">2008.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">72.10</td>
<td style="text-align: right;">144.00</td>
<td style="text-align: right;">3.00</td>
<td style="text-align: right;">3.75</td>
<td style="text-align: right;">64.91</td>
</tr>
<tr class="odd">
<td style="text-align: left;">mean</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">2007.52</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">69.22</td>
<td style="text-align: right;">164.80</td>
<td style="text-align: right;">30.30</td>
<td style="text-align: right;">4.60</td>
<td style="text-align: right;">738.25</td>
</tr>
<tr class="even">
<td style="text-align: left;">SE.mean</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.09</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.18</td>
<td style="text-align: right;">2.30</td>
<td style="text-align: right;">2.18</td>
<td style="text-align: right;">0.08</td>
<td style="text-align: right;">36.68</td>
</tr>
<tr class="odd">
<td style="text-align: left;">CI.mean</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.17</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.35</td>
<td style="text-align: right;">4.50</td>
<td style="text-align: right;">4.27</td>
<td style="text-align: right;">0.15</td>
<td style="text-align: right;">71.91</td>
</tr>
<tr class="even">
<td style="text-align: left;">var</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">21.29</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">90.70</td>
<td style="text-align: right;">15448.52</td>
<td style="text-align: right;">13906.66</td>
<td style="text-align: right;">16.42</td>
<td style="text-align: right;">3951805.48</td>
</tr>
<tr class="odd">
<td style="text-align: left;">std.dev</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">4.61</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">9.52</td>
<td style="text-align: right;">124.29</td>
<td style="text-align: right;">117.93</td>
<td style="text-align: right;">4.05</td>
<td style="text-align: right;">1987.91</td>
</tr>
<tr class="even">
<td style="text-align: left;">coef.var</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: left;">NA</td>
<td style="text-align: right;">0.14</td>
<td style="text-align: right;">0.75</td>
<td style="text-align: right;">3.89</td>
<td style="text-align: right;">0.88</td>
<td style="text-align: right;">2.69</td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col style="width: 7%" />
<col style="width: 9%" />
<col style="width: 10%" />
<col style="width: 8%" />
<col style="width: 14%" />
<col style="width: 8%" />
<col style="width: 14%" />
<col style="width: 8%" />
<col style="width: 7%" />
<col style="width: 10%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;"></th>
<th style="text-align: right;">Hepatitis.B</th>
<th style="text-align: right;">Measles</th>
<th style="text-align: right;">BMI</th>
<th style="text-align: right;">under.five.deaths</th>
<th style="text-align: right;">Polio</th>
<th style="text-align: right;">Total.expenditure</th>
<th style="text-align: right;">Diphtheria</th>
<th style="text-align: right;">HIV.AIDS</th>
<th style="text-align: right;">GDP</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">nbr.val</td>
<td style="text-align: right;">2385.00</td>
<td style="text-align: right;">2938.00</td>
<td style="text-align: right;">2904.00</td>
<td style="text-align: right;">2938.00</td>
<td style="text-align: right;">2919.00</td>
<td style="text-align: right;">2712.00</td>
<td style="text-align: right;">2919.00</td>
<td style="text-align: right;">2938.00</td>
<td style="text-align: right;">2490.00</td>
</tr>
<tr class="even">
<td style="text-align: left;">nbr.null</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">983.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">785.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.00</td>
</tr>
<tr class="odd">
<td style="text-align: left;">nbr.na</td>
<td style="text-align: right;">553.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">34.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">19.00</td>
<td style="text-align: right;">226.00</td>
<td style="text-align: right;">19.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">448.00</td>
</tr>
<tr class="even">
<td style="text-align: left;">min</td>
<td style="text-align: right;">1.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">1.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">3.00</td>
<td style="text-align: right;">0.37</td>
<td style="text-align: right;">2.00</td>
<td style="text-align: right;">0.10</td>
<td style="text-align: right;">1.68</td>
</tr>
<tr class="odd">
<td style="text-align: left;">max</td>
<td style="text-align: right;">99.00</td>
<td style="text-align: right;">212183.00</td>
<td style="text-align: right;">87.30</td>
<td style="text-align: right;">2500.00</td>
<td style="text-align: right;">99.00</td>
<td style="text-align: right;">17.60</td>
<td style="text-align: right;">99.00</td>
<td style="text-align: right;">50.60</td>
<td style="text-align: right;">119172.74</td>
</tr>
<tr class="even">
<td style="text-align: left;">range</td>
<td style="text-align: right;">98.00</td>
<td style="text-align: right;">212183.00</td>
<td style="text-align: right;">86.30</td>
<td style="text-align: right;">2500.00</td>
<td style="text-align: right;">96.00</td>
<td style="text-align: right;">17.23</td>
<td style="text-align: right;">97.00</td>
<td style="text-align: right;">50.50</td>
<td style="text-align: right;">119171.06</td>
</tr>
<tr class="odd">
<td style="text-align: left;">sum</td>
<td style="text-align: right;">193043.00</td>
<td style="text-align: right;">7108762.00</td>
<td style="text-align: right;">111284.90</td>
<td style="text-align: right;">123501.00</td>
<td style="text-align: right;">240964.00</td>
<td style="text-align: right;">16104.37</td>
<td style="text-align: right;">240304.00</td>
<td style="text-align: right;">5118.30</td>
<td style="text-align: right;">18633064.59</td>
</tr>
<tr class="even">
<td style="text-align: left;">median</td>
<td style="text-align: right;">92.00</td>
<td style="text-align: right;">17.00</td>
<td style="text-align: right;">43.50</td>
<td style="text-align: right;">4.00</td>
<td style="text-align: right;">93.00</td>
<td style="text-align: right;">5.75</td>
<td style="text-align: right;">93.00</td>
<td style="text-align: right;">0.10</td>
<td style="text-align: right;">1766.95</td>
</tr>
<tr class="odd">
<td style="text-align: left;">mean</td>
<td style="text-align: right;">80.94</td>
<td style="text-align: right;">2419.59</td>
<td style="text-align: right;">38.32</td>
<td style="text-align: right;">42.04</td>
<td style="text-align: right;">82.55</td>
<td style="text-align: right;">5.94</td>
<td style="text-align: right;">82.32</td>
<td style="text-align: right;">1.74</td>
<td style="text-align: right;">7483.16</td>
</tr>
<tr class="even">
<td style="text-align: left;">SE.mean</td>
<td style="text-align: right;">0.51</td>
<td style="text-align: right;">211.56</td>
<td style="text-align: right;">0.37</td>
<td style="text-align: right;">2.96</td>
<td style="text-align: right;">0.43</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.44</td>
<td style="text-align: right;">0.09</td>
<td style="text-align: right;">285.98</td>
</tr>
<tr class="odd">
<td style="text-align: left;">CI.mean</td>
<td style="text-align: right;">1.01</td>
<td style="text-align: right;">414.82</td>
<td style="text-align: right;">0.73</td>
<td style="text-align: right;">5.80</td>
<td style="text-align: right;">0.85</td>
<td style="text-align: right;">0.09</td>
<td style="text-align: right;">0.86</td>
<td style="text-align: right;">0.18</td>
<td style="text-align: right;">560.78</td>
</tr>
<tr class="even">
<td style="text-align: left;">var</td>
<td style="text-align: right;">628.51</td>
<td style="text-align: right;">131498338.34</td>
<td style="text-align: right;">401.76</td>
<td style="text-align: right;">25742.77</td>
<td style="text-align: right;">548.87</td>
<td style="text-align: right;">6.24</td>
<td style="text-align: right;">562.49</td>
<td style="text-align: right;">25.78</td>
<td style="text-align: right;">203637733.04</td>
</tr>
<tr class="odd">
<td style="text-align: left;">std.dev</td>
<td style="text-align: right;">25.07</td>
<td style="text-align: right;">11467.27</td>
<td style="text-align: right;">20.04</td>
<td style="text-align: right;">160.45</td>
<td style="text-align: right;">23.43</td>
<td style="text-align: right;">2.50</td>
<td style="text-align: right;">23.72</td>
<td style="text-align: right;">5.08</td>
<td style="text-align: right;">14270.17</td>
</tr>
<tr class="even">
<td style="text-align: left;">coef.var</td>
<td style="text-align: right;">0.31</td>
<td style="text-align: right;">4.74</td>
<td style="text-align: right;">0.52</td>
<td style="text-align: right;">3.82</td>
<td style="text-align: right;">0.28</td>
<td style="text-align: right;">0.42</td>
<td style="text-align: right;">0.29</td>
<td style="text-align: right;">2.91</td>
<td style="text-align: right;">1.91</td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col style="width: 8%" />
<col style="width: 12%" />
<col style="width: 20%" />
<col style="width: 18%" />
<col style="width: 30%" />
<col style="width: 9%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;"></th>
<th style="text-align: right;">Population</th>
<th style="text-align: right;">thinness..1.19.years</th>
<th style="text-align: right;">thinness.5.9.years</th>
<th style="text-align: right;">Income.composition.of.resources</th>
<th style="text-align: right;">Schooling</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">nbr.val</td>
<td style="text-align: right;">2.286000e+03</td>
<td style="text-align: right;">2904.00</td>
<td style="text-align: right;">2904.00</td>
<td style="text-align: right;">2771.00</td>
<td style="text-align: right;">2775.00</td>
</tr>
<tr class="even">
<td style="text-align: left;">nbr.null</td>
<td style="text-align: right;">0.000000e+00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">130.00</td>
<td style="text-align: right;">28.00</td>
</tr>
<tr class="odd">
<td style="text-align: left;">nbr.na</td>
<td style="text-align: right;">6.520000e+02</td>
<td style="text-align: right;">34.00</td>
<td style="text-align: right;">34.00</td>
<td style="text-align: right;">167.00</td>
<td style="text-align: right;">163.00</td>
</tr>
<tr class="even">
<td style="text-align: left;">min</td>
<td style="text-align: right;">3.400000e+01</td>
<td style="text-align: right;">0.10</td>
<td style="text-align: right;">0.10</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.00</td>
</tr>
<tr class="odd">
<td style="text-align: left;">max</td>
<td style="text-align: right;">1.293859e+09</td>
<td style="text-align: right;">27.70</td>
<td style="text-align: right;">28.60</td>
<td style="text-align: right;">0.95</td>
<td style="text-align: right;">20.70</td>
</tr>
<tr class="even">
<td style="text-align: left;">range</td>
<td style="text-align: right;">1.293859e+09</td>
<td style="text-align: right;">27.60</td>
<td style="text-align: right;">28.50</td>
<td style="text-align: right;">0.95</td>
<td style="text-align: right;">20.70</td>
</tr>
<tr class="odd">
<td style="text-align: left;">sum</td>
<td style="text-align: right;">2.915422e+10</td>
<td style="text-align: right;">14054.50</td>
<td style="text-align: right;">14143.40</td>
<td style="text-align: right;">1738.94</td>
<td style="text-align: right;">33280.00</td>
</tr>
<tr class="even">
<td style="text-align: left;">median</td>
<td style="text-align: right;">1.386542e+06</td>
<td style="text-align: right;">3.30</td>
<td style="text-align: right;">3.30</td>
<td style="text-align: right;">0.68</td>
<td style="text-align: right;">12.30</td>
</tr>
<tr class="odd">
<td style="text-align: left;">mean</td>
<td style="text-align: right;">1.275338e+07</td>
<td style="text-align: right;">4.84</td>
<td style="text-align: right;">4.87</td>
<td style="text-align: right;">0.63</td>
<td style="text-align: right;">11.99</td>
</tr>
<tr class="even">
<td style="text-align: left;">SE.mean</td>
<td style="text-align: right;">1.276080e+06</td>
<td style="text-align: right;">0.08</td>
<td style="text-align: right;">0.08</td>
<td style="text-align: right;">0.00</td>
<td style="text-align: right;">0.06</td>
</tr>
<tr class="odd">
<td style="text-align: left;">CI.mean</td>
<td style="text-align: right;">2.502396e+06</td>
<td style="text-align: right;">0.16</td>
<td style="text-align: right;">0.16</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.13</td>
</tr>
<tr class="even">
<td style="text-align: left;">var</td>
<td style="text-align: right;">3.722476e+15</td>
<td style="text-align: right;">19.54</td>
<td style="text-align: right;">20.33</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">11.28</td>
</tr>
<tr class="odd">
<td style="text-align: left;">std.dev</td>
<td style="text-align: right;">6.101210e+07</td>
<td style="text-align: right;">4.42</td>
<td style="text-align: right;">4.51</td>
<td style="text-align: right;">0.21</td>
<td style="text-align: right;">3.36</td>
</tr>
<tr class="even">
<td style="text-align: left;">coef.var</td>
<td style="text-align: right;">4.780000e+00</td>
<td style="text-align: right;">0.91</td>
<td style="text-align: right;">0.93</td>
<td style="text-align: right;">0.34</td>
<td style="text-align: right;">0.28</td>
</tr>
</tbody>
</table>

W zbiorze występują zmiene tekstowe: Nazwa kraju oraz status. Drugi z
nich zawiera tylko dwie wartości, więc można te wartości enumeruować w
do analizy.

    data$Status <- sapply(data$Status == "Developed", as.numeric)

Udział wierszy z NA w zbiorze jest wysoki.

    nrow( data[complete.cases(data), ]) / nrow(data)

    ## [1] 0.5612662

W związku z tym zbadano, dla jakich kolumn najczęściej występują
wybrakowane dane oraz porównano je z korelacją między atrybutami.

    corrupted_data <- data %>% 
      summarise_all(list(~sum(is.na(.))/length(.))) %>%
      gather(key="column", value="corrupted") %>%
      filter(corrupted > 0) %>%
      ggplot(aes(x=reorder(column, corrupted), y=corrupted)) +
      geom_bar(stat="identity") +
      coord_flip()

    corrupted_data

![](Raport_files/figure-markdown_strict/missing_plot-1.png)

    corr.data <- data[complete.cases(data),]
    numerics <- attributes(corr.data[unlist(lapply(corr.data, is.numeric))])$names
    cor_mat <- cor(corr.data[numerics])

    corrplot(cor_mat, type = "upper", order = "hclust", 
             tl.col = "black", tl.srt=45, tl.cex=10/ncol(cor_mat))

![](Raport_files/figure-markdown_strict/unnamed-chunk-9-1.png) Na
podstawie powyższych wykresów widać, że najczęściej brakuje danych
dotyczących populacji kraju. Ponieważ ta kolumna jest słabo skorelowana
z pozostałymi kolumnami (śmiertelność wśród dzieci i noworodków jest
wyrażona bezwzględnie, co tłumaczy zwiększoną korelację). Populacja nie
będzie zatem brana pod uwagę podczas dalszej analizy. Kolumny “thinnes…”
wykazują również bardzo wysoką korelację, bliską jedynki. Kolumna
“thinnes.5.9.years” zostanie również usunięta jako zbędna. Kolumny
“Percentage Expenditure” i “GDP” również są skorelowane, a danych na
temat PKB brakuje w ponad 15% wierszy

    data <- data[ , !(names(data) %in% c("Population", "thinness.5.9.years", "GDP") )]
    nrow( data[complete.cases(data), ]) / nrow(data)

    ## [1] 0.7089857

Udział kompletnych wierszy w zbiorze wzrósł z ok. 56,1 % do 70,8%.
Dalszej analizie będą podlegać pozostałe kompletne przypadki.

    data <- data[complete.cases(data),]

wykresy rozkładów wartości atrybutów
------------------------------------

    d <- melt(data[,-c(1:3)])

    ## No id variables; using all as measure variables

    ggplot(d,aes(x = value)) + 
        facet_wrap(~variable, scales = "free", ncol=3, nrow=10) + 
        geom_histogram(bins=50)

![](Raport_files/figure-markdown_strict/distributions-1.png)

Mozna wyróżnić atrybuty o rozkladzie bliskim rozkładowi Pareta, np.:
Percentage expenditure, Measles, Infant deaths. Dla innej grupy
atrybutów, wartości przypominają przesunięty rozkład normalny.
Najbardziej nieregularny jest BMI.

Oczekiwana długość życia w poszczególnych krajach
-------------------------------------------------

(w trybie aktywnym )

\#\#Regresor przewidujący długość życia

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

    ##       [,1]                              [,2]                 
    ##  [1,] "Income.composition.of.resources" "0.485199129939787"  
    ##  [2,] "Life.expectancy"                 "0.461997758998933"  
    ##  [3,] "BMI"                             "0.433494935236889"  
    ##  [4,] "Schooling"                       "0.271898422897695"  
    ##  [5,] "Alcohol"                         "0.266805570999156"  
    ##  [6,] "Diphtheria"                      "0.235216514230498"  
    ##  [7,] "percentage.expenditure"          "0.228104051757919"  
    ##  [8,] "Polio"                           "0.0961617472313969" 
    ##  [9,] "Hepatitis.B"                     "0.00121726621303159"
    ## [10,] "Measles"                         "-0.0170407588196203"
    ## [11,] "infant.deaths"                   "-0.0501780260741767"
    ## [12,] "under.five.deaths"               "-0.0595622396702148"
    ## [13,] "Status"                          "-0.0712235058520618"
    ## [14,] "Total.expenditure"               "-0.167810573177547" 
    ## [15,] "Year"                            "-0.419424289266778" 
    ## [16,] "thinness..1.19.years"            "-0.434078581716779" 
    ## [17,] "HIV.AIDS"                        "-0.443803283286084" 
    ## [18,] "Adult.Mortality"                 "-0.765572356372191"

    ## [1] "R^2 na zbiorze uczącym: 69.610 %"

    ## [1] "R^2 na zbiorze testowym: 72.450 %"

    ## [1] "RMSE na zbiorze uczącym: 11.237 %"

    ## [1] "RMSE na zbiorze testowym: 11.205 %"
