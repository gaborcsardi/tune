# tune (development version)

* The `.notes` column now contains information on the type of note (error or warning), the location where it occurred, and the note. Printing a tune result has different output describing the notes. 

* `collect_notes()` can be used to gather any notes to a tibble. (#363)

* Parallel processing with PSOCK clusters is now more efficient, due to carefully avoiding sending extraneous information to each worker (#384, #396).

* The engine arguments for xgboost `alpha`, `lambda`, and `scale_pos_weight` are now tunable.

# tune 0.1.6

* When using `load_pkgs()`, packages that use random numbers on start-up do not affect the state of the RNG. We also added more control of the RNGkind to make it consistent with the user's previous value (#389). 

* New `extract_*()` functions have been added that supersede many of the the existing `pull_*()` functions. This is part of a larger move across the tidymodels packages towards a family of generic `extract_*()` functions. Many `pull_*()` functions have been soft-deprecated, and will eventually be removed. (#378)

# tune 0.1.5

* Fixed a bug where the resampled confusion matrix is transposed when `conf_mat_resamped(tidy = FALSE)` (#372)

* False positive warnings no longer occur when using the `doFuture` package for parallel processing (#377)

# tune 0.1.4

* Fixed an issue in `finalize_recipe()` which failed during tuning of recipe steps that contain multiple `tune()` parameters in an single step.

* Changed `conf_mat_resampled()` to return the same type of object as `yardstick::conf_mat()` when `tidy = FALSE` (#370).

* The automatic parameter machinery for `sample_size` with the C5.0 engine was changes to use `dials::sample_prop()`. 

# tune 0.1.3

* The `rsample::pretty()` methods were extended to `tune_results` objects.  

* Added `pillar` methods for formatting `tune` objects in list columns. 

* A method for `.get_fingerprint()` was added. This helps determine if `tune` objects used the same resamples. 

# tune 0.1.2

* `collect_predictions()` was made generic. 
 
* The default tuning parameter for the SVM polynomial degree was switched from `dials::degree()` to `dials::prod_degree()` since it must be an integer. 

## Bug Fixes

* `last_fit()` and `workflows::fit()` will now give identical results for the same workflow when the underlying model uses random number generation (#300).

* Fixed an issue where recipe tuning parameters could be randomly matched to the tuning grid incorrectly (#316).

* `last_fit()` no longer accidentally adjusts the random seed (#264).

* Fixed two bugs in the acquisition function calculations.

## Other Changes

* New `parallel_over` control argument to adjust the parallel processing method that tune uses.

* The `.config` column that appears in the returned tibble from tuning and fitting resamples has changed slightly. It is now always of the form `"Preprocessor<i>_Model<j>"`.

* `predict()` can now be called on the workflow returned from `last_fit()` (#294, #295, #296).

* tune now supports setting the `event_level` option from yardstick through the control objects (i.e. `control_grid(event_level = "second")`) (#240, #249).

* tune now supports workflows created with the new `workflows::add_variables()` preprocessor.

* Better control the random number streams in parallel for `tune_grid()` and `fit_resamples()` (#11)

* Allow `...` to pass options from `tune_bayes()` to `GPfit::GP_fit()`. 

* Additional checks are done for the initial grid that is given to `tune_bayes()`. If the initial grid is small relative to the number of model terms, a warning is issued. If the grid is a single point, an error occurs. (#269)

* Formatting of some messages created by `tune_bayes()` now respect the width and wrap lines using the new `message_wrap()` function. 

* tune functions (`tune_grid()`, `tune_bayes()`, etc) will now error if a model specification or model workflow are given as the first argument (the soft deprecation period is over). 

* An `augment()` method was added for objects generated by `tune_*()`, `fit_resamples()`, and `last_fit()`.  

# tune 0.1.1

## Breaking Changes

* `autoplot.tune_results()` now requires objects made by version 0.1.0 or higher of tune. 

* `tune` objects no longer keep the `rset` class that they have from the `resamples` argument. 

## Other Changes

* `autoplot.tune_results()` now produces a different plot when the tuning grid is a regular grid (i.e. factorial or nearly factorial in nature). If there are 5+ parameters, the standard plot is produced. Non-regular grids are plotted in the same way (although see next bullet point). See `?autoplot.tune_results` for more information.

* `autoplot.tune_results()` now transforms the parameter values for the plot. For example, if the `penalty` parameter was used for a regularized regression, the points are plotted on the log-10 scale (its default transformation). For non-regular grids, the facet labels show the transformation type (e.g. `"penalty (log-10)"` or `"cost (log-2)"`). For regular grid, the x-axis is scaled using `scale_x_continuous()`. 

* Finally, `autoplot.tune_results()` now shows the parameter _labels_ in a plot. For example, if a k-nearest neighbors model was used with `neighbors = tune()`, the parameter will be labeled as `"# Nearest Neighbors"`. When an ID was used, such as `neighbors = tune("K")`, this is used to identify the parameter. 

* In other plotting news, `coord_obs_pred()` has been included for regression models. When plotting the observed and predicted values from a model, this forces the x- and y-axis to be the same range and uses an aspect ratio of 1. 

* The outcome names are saved in an attribute called `outcomes` to objects with class `tune_results`. Also, several accessor functions (named `.get_tune_*()) were added to more easily access such attributes.  

* `conf_mat_resampled()` computes the average confusion matrix across resampling statistics for a single model.  

* `show_best()`, and the `select_*()` functions will now use the first metric in the metric set if no metric is supplied. 

* `filter_parameters()` can trim the `.metrics` column of unwanted results (as well as columns `.predictions` and `.extracts`) from `tune_*` objects. 

* In concert with `dials` > 0.0.7, tuning engine-specific arguments is possible. Many known engine-specific tuning parameters and handled automatically. 

* If a grid is given, parameters do not need to be finalized to be used in the `tune_*()` functions. 

* Added a `save_workflow` argument to `control_*` functions that will result in the workflow object used to carry out tuning/fitting (regardless of whether a formula or recipe was given as input to the function) to be appended to the resulting `tune_results` object in a `workflow` attribute. The new `.get_tune_workflow()` function can be used to access the workflow.

* Many of the output columns in a `tune_results` object have an additional column called `.config`. This is meant to be a unique, qualitative value that used for sorting and merging. These values also correspond to the messages in the logging produced when `verbose = TRUE`. 

# tune 0.1.0

## Breaking Changes

* The arguments to the main tuning/fitting functions (`tune_grid()`, `tune_bayes()`, etc) have been reordered to better align with parsnip's `fit()`. The first argument to all these functions is now a model specification or model workflow. The previous versions are soft-deprecated as of 0.1.0 and will be deprecated as of 0.1.2.

## Other Changes

* Added more packages to be fully loaded in the workers when run in parallel using `doParallel` (#157),  (#159), and  (#160)

* `collect_predictions()` gains two new arguments. `parameters` allows for pre-filtering of the hold-out predictions by tuning parameters values. If you are only interested in one sub-model, this makes things much faster. The other option is `summarize` and is used when the resampling method has training set rows that are predicted in multiple holdout sets.  

* `select_best()`, `select_by_one_std_err()`, and `select_by_pct_loss()` no longer have a redundant `maximize` argument (#176). Each metric set in yardstick now has a direction (maximize vs. minimize) built in.

## Bug Fixes

* `tune_bayes()` no longer errors with a recipe, which has tuning parameters, in combination with a parameter set, where the defaults contain unknown values (#168).

# tune 0.0.1

* CRAN release.

* Changed license to MIT

# tune 0.0.0.9002

* The `...` arguments of `tune_grid()` and `tune_bayes()` have been moved
  forward to force optional arguments to be named.

* New `fit_resamples()` for fitting a set of resamples that don't require any
  tuning.

* Changed `summarise.tune_results()` back to `estimate.tune_results()`

# tune 0.0.0.9000

* Added a `NEWS.md` file to track changes to the package.
