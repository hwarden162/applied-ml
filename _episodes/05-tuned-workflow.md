---
title: "Hyperparameter Tuning"
teaching: 0
exercises: 0
questions:
- "Write something here."
objectives:
- "Write something here."
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---

## Hyperparameter Tuning

Linear regression is considered to be a simpler model both due to its constraints and that it has no hyperparameters to tune. If you remember from a previous session, models have parameters and hyperparameters. Parameters are learnt by the model from the data but hyperparameters have to be set by us. Luckily, `tidymodels` has a lot of tools that we can use for finding the best hyperparameters and justifying the choices we make.

Hyperparameter tuning is performed in `tidymodels` using a package called `dials`. We are going to demonstrate how to use `dials` by training a random forest model, we aren't going to go into the details of how random forests work but you can see we will be able to train one anyway! When it comes to applying this in the real world, you should always research the model you are using and the basics on how it works to see if it appropriate for your use case.

> ## Identify the Hyperparameters
>
> Use `?rand_forest` to look at the help documentation for creating a random forest model. What are the hyperparameters for the model?
{: .challenge}

Firstly, lets create a new workflow object and add our recipe to it. Note how this is exactly the same as before and we are reusing the same preprocessor

```r
housing_wf_rfrst <- workflow() |> 
  add_recipe(housing_rec)
```

```
══ Workflow ════════════════════════════════════════════════════════════════════════════════
Preprocessor: Recipe
Model: None

── Preprocessor ────────────────────────────────────────────────────────────────────────────
4 Recipe Steps

• step_log()
• step_nzv()
• step_normalize()
• step_dummy()
```
{: .output}

Now we can create our model. We are going to use the `rand_forest()` function from the `parsnip` package to create a random forest model object. Rather than setting the values of the hyperparameters, we are going to set them equal to the `tune()` function. This lets `tidymodels` know that we want to tune these hyperparameters and that this isn't the final version of the model. You can see that this time we haven't selected an engine to use but it has defaulted to the `ranger` package.

```r
housing_rfrst <- rand_forest(
  mode = "regression",
  mtry = tune(),
  trees = tune(),
  min_n = tune()
)

housing_rfrst
```

```
Random Forest Model Specification (regression)

Main Arguments:
  mtry = tune()
  trees = tune()
  min_n = tune()

Computational engine: ranger 
```

We can now add this model to our workflow like before.

```r
housing_wf_rfrst <- workflow() |> 
  add_recipe(housing_rec) |> 
  add_model(housing_rfrst)

housing_wf_rfrst
```

```
══ Workflow ════════════════════════════════════════════════════════════════════════════════
Preprocessor: Recipe
Model: rand_forest()

── Preprocessor ────────────────────────────────────────────────────────────────────────────
4 Recipe Steps

• step_log()
• step_nzv()
• step_normalize()
• step_dummy()

── Model ───────────────────────────────────────────────────────────────────────────────────
Random Forest Model Specification (regression)

Main Arguments:
  mtry = tune()
  trees = tune()
  min_n = tune()

Computational engine: ranger
```
{: .output}


Next we are going to perform cross validation to select the best combination of hyperparameters to use to train the model and to do this we need to decide the combinations to test. Thankfully, `tidymodels` can do this for us. We can use the `extract_parameter_set_dials()` to extract the parameters that need tuning from the model *as well as what type of parameters they are*.

```r
tuning_params <- housing_rfrst |> 
  extract_parameter_set_dials()

 tuning_params 
```

```
Collection of 3 parameters for tuning

 identifier  type    object
       mtry  mtry nparam[?]
      trees trees nparam[+]
      min_n min_n nparam[+]

Model parameters needing finalization:
   # Randomly Selected Predictors ('mtry')

See `?dials::finalize` or `?dials::update.parameters` for more information.
```
{: .output}

You can see in the output that some hyperparameters need `finalization`. The `tidymodels` package is good at guessing different types of parameters but sometimes it needs some extra context. For example, the `mtry` parameter is the number of randomly selected predictors to use at each split which is dependent in the data set we are using (smaller datasets won't need as many) and so to get a set of reasonable values we can show the model some example data and it can use this to estimate a good parameter range. We do this using the `finalize()` function, passing through our training data set.

```r
tuning_params <- housing_rfrst |> 
  extract_parameter_set_dials() |> 
  finalize(housing_train)

tuning_params
```

```
Collection of 3 parameters for tuning

 identifier  type    object
       mtry  mtry nparam[+]
      trees trees nparam[+]
      min_n min_n nparam[+]
```
{: .output}

{% include links.md %}

