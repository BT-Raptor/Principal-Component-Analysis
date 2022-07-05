Thingamajig
================
BT_Raptor
21/04/2022

``` r
require(data.table)
```

    ## Loading required package: data.table

``` r
require(stats)
require(factoextra)
```

    ## Loading required package: factoextra

    ## Warning: package 'factoextra' was built under R version 4.1.3

    ## Loading required package: ggplot2

    ## Warning: package 'ggplot2' was built under R version 4.1.3

    ## Welcome! Want to learn more? See two factoextra-related books at https://goo.gl/ve3WBa

``` r
setwd("C:/Users/ricke/Desktop/Raptor's Data Science/")
options(scipen=999)
```

Data Acquisition

``` r
all_data=fread("opp.csv")
all_data=na.omit(all_data)
head(all_data)
```

    ##    case site Pop sex age hdlngth skullw totlngth taill footlgth earconch  eye
    ## 1:    1    1 Vic   m   8    94.1   60.4     89.0  36.0     74.5     54.5 15.2
    ## 2:    2    1 Vic   f   6    92.5   57.6     91.5  36.5     72.5     51.2 16.0
    ## 3:    3    1 Vic   f   6    94.0   60.0     95.5  39.0     75.4     51.9 15.5
    ## 4:    4    1 Vic   f   6    93.2   57.1     92.0  38.0     76.1     52.2 15.2
    ## 5:    5    1 Vic   f   2    91.5   56.3     85.5  36.0     71.0     53.2 15.1
    ## 6:    6    1 Vic   f   1    93.1   54.8     90.5  35.5     73.2     53.6 14.2
    ##    chest belly
    ## 1:  28.0    36
    ## 2:  28.5    33
    ## 3:  30.0    34
    ## 4:  28.0    34
    ## 5:  28.5    33
    ## 6:  30.0    32

Creating a new dataframe, where we do the column tweaking

``` r
all_data=all_data[,`:=`(case=NULL,site=NULL,Pop=NULL,sex=NULL)]
head(all_data)
```

    ##    age hdlngth skullw totlngth taill footlgth earconch  eye chest belly
    ## 1:   8    94.1   60.4     89.0  36.0     74.5     54.5 15.2  28.0    36
    ## 2:   6    92.5   57.6     91.5  36.5     72.5     51.2 16.0  28.5    33
    ## 3:   6    94.0   60.0     95.5  39.0     75.4     51.9 15.5  30.0    34
    ## 4:   6    93.2   57.1     92.0  38.0     76.1     52.2 15.2  28.0    34
    ## 5:   2    91.5   56.3     85.5  36.0     71.0     53.2 15.1  28.5    33
    ## 6:   1    93.1   54.8     90.5  35.5     73.2     53.6 14.2  30.0    32

Training & Test Split

``` r
train_dt=all_data[seq(1,52),]
train_y=train_dt$age
train_dt[,age:=NULL]
test_dt=all_data[seq(53,104),]
test_y=test_dt$age
test_dt[,age:=NULL]
```

Training the PCA

``` r
pca_model=prcomp(train_dt,scale=T)
fviz_eig(pca_model)
```

![](R-Markdown-fun-file_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Using the PCA for predictions

``` r
pca_tweaked_data=predict(pca_model,test_dt)
pca_tweaked_data=pca_tweaked_data[,c(1,2,3)]
head(pca_tweaked_data)
```

    ##             PC1          PC2       PC3
    ## [1,] -2.6220931 -1.612369053 0.8605280
    ## [2,] -3.0770432 -1.172660936 0.9045833
    ## [3,] -2.1159464 -1.427951445 0.2796744
    ## [4,] -4.8506676 -0.003551313 0.2259565
    ## [5,]  0.8949091 -2.370232185 2.0755078
    ## [6,] -0.1567513 -3.145549370 1.1074402

In a real life scenario: you will first apply the prediction on the
training data, then train the linear model on the 3 principal
components. Then you will predict the pca on the test data, then predict
the linear model on the newly tweaked test data.
