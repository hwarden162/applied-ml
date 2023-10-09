---
title: "Data Preparation"
teaching: 0
exercises: 0
questions:
- "How do you split your data?"
- "What is a recipe?"
objectives:
- "Write something here."
keypoints:
- "You can use `rsample` to split your data into training and testing sets."
- "You can use `recipes` to create a recipe to preprocess your data."
---

## Splitting Your Data

### Test/Train Split

```r
housing_split <- housing |> 
  initial_split(prop = 0.8)

housing_train <- training(housing_split)

housing_test <- testing(housing_split)

housing_train
```

```
# A tibble: 404 × 14
      crim    zn indus chas    nox    rm   age   dis   rad   tax ptratio     b lstat  medv
     <dbl> <dbl> <dbl> <fct> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>   <dbl> <dbl> <dbl> <dbl>
 1  0.207      0 27.7  0     0.609  5.09  98    1.82     4   711    20.1 318.  29.7    8.1
 2 11.8        0 18.1  0     0.718  6.82  76.5  1.79    24   666    20.2  48.4 22.7    8.4
 3  0.142      0  6.91 0     0.448  6.17   6.6  5.72     3   233    17.9 383.   5.81  25.3
 4  0.0883     0 10.8  0     0.413  6.42   6.6  5.29     4   305    19.2 384.   6.72  24.2
 5  0.127      0  6.91 0     0.448  6.77   2.9  5.72     3   233    17.9 385.   4.84  26.6
 6  0.0918     0  4.05 0     0.51   6.42  84.1  2.65     5   296    16.6 396.   9.04  23.6
 7  0.0178    95  1.47 0     0.403  7.14  13.9  7.65     3   402    17   384.   4.45  32.9
 8  4.67       0 18.1  0     0.713  5.98  87.9  2.58    24   666    20.2  10.5 19.0   12.7
 9  0.141      0 13.9  0     0.437  5.79  58    6.32     4   289    16   397.  15.8   20.3
10  0.0689     0  2.46 0     0.488  6.14  62.2  2.60     3   193    17.8 397.   9.45  36.2
# ℹ 394 more rows
# ℹ Use `print(n = ...)` to see more rows
```
{: .output}

### Validation Splits

```r
housing_folds <- vfold_cv(housing_train, v = 5, repeats = 3)

housing_folds
```

```
#  5-fold cross-validation repeated 3 times 
# A tibble: 15 × 3
   splits           id      id2  
   <list>           <chr>   <chr>
 1 <split [323/81]> Repeat1 Fold1
 2 <split [323/81]> Repeat1 Fold2
 3 <split [323/81]> Repeat1 Fold3
 4 <split [323/81]> Repeat1 Fold4
 5 <split [324/80]> Repeat1 Fold5
 6 <split [323/81]> Repeat2 Fold1
 7 <split [323/81]> Repeat2 Fold2
 8 <split [323/81]> Repeat2 Fold3
 9 <split [323/81]> Repeat2 Fold4
10 <split [324/80]> Repeat2 Fold5
11 <split [323/81]> Repeat3 Fold1
12 <split [323/81]> Repeat3 Fold2
13 <split [323/81]> Repeat3 Fold3
14 <split [323/81]> Repeat3 Fold4
15 <split [324/80]> Repeat3 Fold5
```
{: .output}

## Preprocessing Your Data

```r
library(skimr)

skim(housing_train)
```

```
── Data Summary ────────────────────────
                           Values       
Name                       housing_train
Number of rows             404          
Number of columns          14           
_______________________                 
Column type frequency:                  
  factor                   1            
  numeric                  13           
________________________                
Group variables            None         

── Variable type: factor ─────────────────────────────────────────────────────────────────────────────────
  skim_variable n_missing complete_rate ordered n_unique top_counts   
1 chas                  0             1 FALSE          2 0: 375, 1: 29

── Variable type: numeric ────────────────────────────────────────────────────────────────────────────────
   skim_variable n_missing complete_rate    mean      sd        p0      p25     p50     p75    p100 hist 
 1 crim                  0             1   3.42    7.55    0.00632   0.0861   0.263   3.69   73.5   ▇▁▁▁▁
 2 zn                    0             1  10.4    22.5     0         0        0       3.12  100     ▇▁▁▁▁
 3 indus                 0             1  11.4     6.95    0.74      5.19     9.69   18.1    27.7   ▇▆▁▇▁
 4 nox                   0             1   0.556   0.117   0.389     0.453    0.538   0.624   0.871 ▇▇▅▃▁
 5 rm                    0             1   6.28    0.717   3.56      5.88     6.19    6.62    8.78  ▁▂▇▂▁
 6 age                   0             1  69.0    28.3     2.9      45.8     79.1    94.1   100     ▂▂▂▂▇
 7 dis                   0             1   3.78    2.11    1.13      2.10     3.17    5.14   12.1   ▇▅▂▁▁
 8 rad                   0             1   9.56    8.74    1         4        5      24      24     ▇▂▁▁▃
 9 tax                   0             1 409.    170.    187       280.     330     666     711     ▇▇▃▁▇
10 ptratio               0             1  18.5     2.20   12.6      17       19.1    20.2    22     ▁▃▃▅▇
11 b                     0             1 357.     90.4     0.32    375.     391.    396.    397.    ▁▁▁▁▇
12 lstat                 0             1  12.7     7.22    1.73      7.09    11.2    16.9    38.0   ▇▇▃▂▁
13 medv                  0             1  22.7     9.35    5        17.0     21.2    25      50     ▂▇▃▁▁
```
{: .output}

{% include links.md %}

