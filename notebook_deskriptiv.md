Einführung in die deskriptive Statistik mit R
================
Prof. Dr. Jan Kirenz, Hochschule der Medien

-   [Datenimport und Datenprüfung](#datenimport-und-datenprüfung)
-   [Arithmetischer Mittelwert](#arithmetischer-mittelwert)
-   [Getrimmter arithmetischer Mittelwert](#getrimmter-arithmetischer-mittelwert)
-   [Median](#median)
-   [Varianz](#varianz)
-   [Standardabweichung](#standardabweichung)
-   [Schiefe und Kurtosis](#schiefe-und-kurtosis)
-   [Histogramm](#histogramm)
-   [Wahrscheinlichkeitsdichtefunktion](#wahrscheinlichkeitsdichtefunktion)

### Datenimport und Datenprüfung

Import der Daten von GitHub:

``` r
# install.packages('tidyverse')
library(tidyverse)
Advertising <- read_csv("https://raw.githubusercontent.com/kirenz/datasets/master/advertising.csv")
```

Überblick über die Daten verschaffen (Skalenniveaus prüfen):

``` r
library(tidyverse)
glimpse(Advertising)
```

    ## Observations: 200
    ## Variables: 5
    ## $ X1        <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 1...
    ## $ TV        <dbl> 230.1, 44.5, 17.2, 151.5, 180.8, 8.7, 57.5, 120.2, 8...
    ## $ radio     <dbl> 37.8, 39.3, 45.9, 41.3, 10.8, 48.9, 32.8, 19.6, 2.1,...
    ## $ newspaper <dbl> 69.2, 45.1, 69.3, 58.5, 58.4, 75.0, 23.5, 11.6, 1.0,...
    ## $ sales     <dbl> 22.1, 10.4, 9.3, 18.5, 12.9, 7.2, 11.8, 13.2, 4.8, 1...

-   Beschreibung der relevanten Daten:
    -   Variablen: *TV*, *radio*, *newspaper* enthalten jeweils Werbeausgaben in Tausend Dollar; *sales* = Produkte in Tausend Einheiten
    -   Skalenniveaus: alle Variablen sind metrisch skaliert
    -   Abhängige Variable (dependent variable, response): *sales*
    -   Unabhängige Variablen (independent variables, predictors): *TV*, *radio*, *newspaper*

Für die weiteren Berechnungen wird die Variable X1 nicht benötigt, weshalb wir diese löschen:

``` r
Advertising$X1 <- NULL
```

### Arithmetischer Mittelwert

Bei der Berechnung des *arithmetischen Mittelwerts* in R sollte immer die Anweisung gegeben werden, fehlende Werte auszuschließen ("remove values which are not available"). Ansonsten stoppt R bei fehlenden Werten die Berechnung und gibt eine Fehlermeldung aus.

``` r
mean_sales <- mean(Advertising$sales, na.rm = TRUE)
print(paste0("Mittelwert der Variable Sales: ", mean_sales))
```

    ## [1] "Mittelwert der Variable Sales: 14.0225"

### Getrimmter arithmetischer Mittelwert

Bei dem *getrimmten Mittelwert* wird ein bestimmer Anteil der größten und kleinsten Beobachtungen - hier oberhalb des 90% Quantils und unterhalb des 10 % Quantils - ignoriert. Damit sollen Ausreißer aus der Berechnung des Mittelwerts ausgeschlossen werden. Der getrimmte Mittelwert kann wie folgt in R berechnet werden:

``` r
mean_trim_sales <- mean(Advertising$sales, trim = 0.1, na.rm = TRUE)
print(paste0("Getrimmter Mittelwert der Variable Sales: ", round(mean_trim_sales, 2)))
```

    ## [1] "Getrimmter Mittelwert der Variable Sales: 13.78"

### Median

``` r
median_sales <- median(Advertising$sales, na.rm = TRUE)
print(paste0("Median der Variable Sales: ", median_sales))
```

    ## [1] "Median der Variable Sales: 12.9"

### Varianz

Die **Varianz** einer Stichprobe wird wie folgt berechnet:

$$s^{2} = \\frac{\\sum\_{i=1}^{n} \\left(x\_{i} - \\bar{x}\\right)^{2}} {n-1}$$

``` r
var_sales <- var(Advertising$sales, na.rm = TRUE)
print(paste0("Varianz der Variable Sales: ", round(var_sales, 2)))
```

    ## [1] "Varianz der Variable Sales: 27.22"

### Standardabweichung

Die Standardabweichung ist ein häufig verwendetes Streuungsmaß und beschreibt die mittlere Abweichung der einzelnen Messwerte von dem arithmetischen Mittelwert. Die Standardabweichung ist die positive Wurzel der Varianz.

Berechnung der **Standardabweichung** einer Stichprobe:

$$s = \\sqrt{\\frac{\\sum\\limits\_{i=1}^{n} \\left(x\_{i} - \\bar{x}\\right)^{2}} {n-1}}$$

``` r
sd_sales <-  sd(Advertising$sales, na.rm = TRUE)
print(paste0("Standardabweichung der Variable Sales: ", round(sd_sales,2)))
```

    ## [1] "Standardabweichung der Variable Sales: 5.22"

### Schiefe und Kurtosis

FÜr die Ausgabe einer Übersicht mehrerer deskriptiver Statistiken (u.a. Schiefe und Kurtosis) kann das Paket `psych` genutzt werden:

``` r
# install.packages('psych')
library(psych)
psych::describe(Advertising) 
```

    ##           vars   n   mean    sd median trimmed    mad min   max range
    ## TV           1 200 147.04 85.85 149.75  147.20 108.82 0.7 296.4 295.7
    ## radio        2 200  23.26 14.85  22.90   23.00  19.79 0.0  49.6  49.6
    ## newspaper    3 200  30.55 21.78  25.75   28.41  23.13 0.3 114.0 113.7
    ## sales        4 200  14.02  5.22  12.90   13.78   4.82 1.6  27.0  25.4
    ##            skew kurtosis   se
    ## TV        -0.07    -1.24 6.07
    ## radio      0.09    -1.28 1.05
    ## newspaper  0.88     0.57 1.54
    ## sales      0.40    -0.45 0.37

-   Hinweise zu den Kennzahlen:
    -   vars: Nummer der Variable
    -   n: Anzahl der Beobachtungen
    -   mean: arithmetischer Mittelwert
    -   sd: empirische Standardabweichung
    -   median: Median
    -   trimmed: getrimmter Mittelwert
    -   mad: Mittlere absolute Abweichung vom Median
    -   min: kleinster Beobachtungswert
    -   max: größter Beobachtungswert
    -   range: Spannweite
    -   skew: Schiefe
    -   kurtosis: Wölbung
    -   se = Standardfehler

Die *Schiefe* ist eine statistische Kennzahl, die die Art und Stärke der Asymmetrie einer Wahrscheinlichkeitsverteilung beschreibt. Sie zeigt an, ob und wie stark die Verteilung nach rechts (positive Schiefe, linkssteil, rechtsschief) oder nach links (negative Schiefe, rechtssteil, linksschief) geneigt ist. Jede nicht symmetrische Verteilung heißt schief.

Die Abweichung des Verlaufs einer Verteilung vom Verlauf einer Normalverteilung wird *Kurtosis* (Wölbung) genannt. Sie gibt an, wie spitz die Kurve verläuft. Unterschieden wird zwischen positiver, spitz zulaufender (leptokurtische Verteilung) und negativer, flacher (platykurtische Verteilung) Kurtosis. Die Kurtosis zählt zu den zentralen Momenten einer Verteilung, mittels derer der Kurvenverlauf definiert wird. Eine Kurtosis mit Wert 0 ist normalgipflig (mesokurtisch), ein Wert größer 0 ist steilgipflig und ein Wert unter 0 ist flachgipflig.

### Histogramm

Erstellung eines Histogramms für die Variable *sales*.

``` r
library(ggplot2)
ggplot(Advertising, aes(sales)) +
  geom_histogram(bins=15, color="white")  +
  labs(title="Histogramm für Sales", x="Sales", y="Anzahl") 
```

![](notebook_deskriptiv_files/figure-markdown_github/unnamed-chunk-10-1.png)

### Wahrscheinlichkeitsdichtefunktion

Erstellung einer Wahrscheinlichkeitsdichtefunktion (mit Darstellung verschiedener Kennzahlen) für die Variable *sales*.

``` r
library(ggplot2)
ggplot(Advertising, aes(sales)) +
  geom_density(fill="grey",alpha=.2 ) +
  geom_vline(aes(xintercept=mean(sales, na.rm=TRUE)), color="red", linetype="dotted", size=0.6) +
  geom_vline(aes(xintercept=median(sales, na.rm=TRUE)), color="red", linetype="dotted", size=0.6) +
  geom_text(aes(x=median(sales), y=0.02), colour = "red", size =3,  
             label=round(mean(Advertising$sales), digits=2), hjust=-1) +
  geom_text(aes(x=mean(sales), y=0.02), hjust=-0.7, colour = "red", size = 3, label="Mittelwert") +
  geom_text(aes(x=median(sales), y=0.005), colour = "red", size =3, 
             label=round(median(Advertising$sales), digits=2), hjust=1) +
  geom_text(aes(x=median(sales), y=0.01), colour = "red", size = 3, label="Median", hjust=1) +
  labs(x="Produktabsatz (in Tausend Einheiten)", y = "Dichte", title = "Wahrscheinlichkeitsdichtefunktion") 
```

![](notebook_deskriptiv_files/figure-markdown_github/unnamed-chunk-11-1.png)

In der Abbildung kann man erkennen, dass es sich um eine asymmetrische Verteilung handelt (d.h. es liegt eine Abweichung von der Normalverteilung vor). Konkret handelt es sich um eine rechtsschiefe Verteilung (Mittelwert &gt; Median; Schiefe = + 0.40).
