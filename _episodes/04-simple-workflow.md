---
title: "Building a Simple Workflow"
teaching: 0
exercises: 0
questions:
- "Write something here."
objectives:
- "Write something here."
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---

## Creating a Workflow

```r
housing_wf <- workflow()

housing_wf
```

```
══ Workflow ══════════════════════════════════════════════════════════════════════════════════════════════
Preprocessor: None
Model: None
```
{: .output}

```r
housing_wf <- workflow() |> 
  add_recipe(housing_rec)

housing_wf
```

```
══ Workflow ══════════════════════════════════════════════════════════════════════════════════════════════
Preprocessor: Recipe
Model: None

── Preprocessor ──────────────────────────────────────────────────────────────────────────────────────────
3 Recipe Steps

• step_log()
• step_nzv()
• step_normalize()
```
{: .output}

```r
housing_lin_reg <- linear_reg(
  mode = "regression"
)

housing_lin_reg
```

```
Linear Regression Model Specification (regression)

Computational engine: lm 
```
{: .output}

```r
housing_wf <- workflow() |> 
  add_recipe(housing_rec) |> 
  add_model(housing_lin_reg)

housing_wf
```

```
══ Workflow ══════════════════════════════════════════════════════════════════════════════════════════════
Preprocessor: Recipe
Model: linear_reg()

── Preprocessor ──────────────────────────────────────────────────────────────────────────────────────────
3 Recipe Steps

• step_log()
• step_nzv()
• step_normalize()

── Model ─────────────────────────────────────────────────────────────────────────────────────────────────
Linear Regression Model Specification (regression)

Computational engine: lm 
```
{: .output}

```r
housing_fit <- housing_wf |> 
  fit(housing_train)

housing_fit
```

```
══ Workflow [trained] ════════════════════════════════════════════════════════════════════════════════════
Preprocessor: Recipe
Model: linear_reg()

── Preprocessor ──────────────────────────────────────────────────────────────────────────────────────────
3 Recipe Steps

• step_log()
• step_nzv()
• step_normalize()

── Model ─────────────────────────────────────────────────────────────────────────────────────────────────

Call:
stats::lm(formula = ..y ~ ., data = data)

Coefficients:
(Intercept)         crim           zn        indus        chas1          nox           rm          age  
    22.4596       1.4217       1.3904       0.0857       3.3179      -2.2373       3.0149      -0.3350  
        dis          rad          tax      ptratio            b        lstat  
    -3.2345       1.6938      -2.2585      -1.8018       1.3354      -3.7415  
```
{: .output}

{% include links.md %}

