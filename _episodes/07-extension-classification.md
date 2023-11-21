---
title: "After Training a Model"
teaching: 0
exercises: 0
questions:
- "Write something here."
objectives:
- "Write something here."
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
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

data_knn <- parsnip::nearest_neighbor(
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
```

{% include links.md %}
