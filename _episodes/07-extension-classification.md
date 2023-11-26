---
title: "Full Walkthrough For Classification"
teaching: 0
exercises: 0
questions:
- "Can we apply what we have learnt to a new dataset?"
objectives:
- "Apply what we have learnt to a new dataset."
keypoints:
- "We can apply what we have learnt to a new dataset."
---
```r
data <- iris |> 
  as_tibble()

data_split <- initial_split(data, prop = 0.8, strata = Species)
data_train <- training(data_split)
data_test <- testing(data_split)

data_folds <- vfold_cv(data_train, repeats = 5, strata = Species)

data_rec <- recipe(Species ~ ., data = data_train) |> 
  step_normalize(all_numeric()) |> 
  prep()

data_knn <- nearest_neighbor(
  mode = "classification",
  neighbors = tune(),
  weight_func = tune(),
  dist_power = tune()
)

data_wf <- workflow() |> 
  add_recipe(data_rec) |> 
  add_model(data_knn)

tuning_params <- data_knn |> 
  extract_parameter_set_dials()

data_tuning_grid <- grid_latin_hypercube(
  tuning_params,
  size = 100
)

data_tune <- tune_grid(
  data_wf,
  data_folds,
  tuning_params
)

data_tune |> 
  collect_metrics()

autoplot(data_tune)

best_params <- data_tune |> 
  select_best("roc_auc")

data_last_fit <- data_wf |> 
  finalize_workflow(best_params) |> 
  last_fit(data_split)

data_last_fit |> 
  collect_metrics()

iris_test_res <- data_test |> 
  select(Species) |> 
  bind_cols(
    data_last_fit |> 
      extract_workflow() |> 
      predict(data_test)
  ) |> 
  transmute(
    ref = Species,
    pred = .pred_class 
  )

library(caret)

confusionMatrix(data = iris_test_res$pred, reference = iris_test_res$ref)
```

```
Confusion Matrix and Statistics

            Reference
Prediction   setosa versicolor virginica
  setosa         10          0         0
  versicolor      0          8         2
  virginica       0          2         8

Overall Statistics
                                          
               Accuracy : 0.8667          
                 95% CI : (0.6928, 0.9624)
    No Information Rate : 0.3333          
    P-Value [Acc > NIR] : 2.296e-09       
                                          
                  Kappa : 0.8             
                                          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: setosa Class: versicolor Class: virginica
Sensitivity                 1.0000            0.8000           0.8000
Specificity                 1.0000            0.9000           0.9000
Pos Pred Value              1.0000            0.8000           0.8000
Neg Pred Value              1.0000            0.9000           0.9000
Prevalence                  0.3333            0.3333           0.3333
Detection Rate              0.3333            0.2667           0.2667
Detection Prevalence        0.3333            0.3333           0.3333
Balanced Accuracy           1.0000            0.8500           0.8500
```
{: .output}

{% include links.md %}
