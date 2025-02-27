## R data validation

For those familiar with R (but not python),
there is a separate set of tests that **may** be useful to diagnose data 
formatting issues in
[functions_plausibility.R](../code/validation/R-scripts/functions_plausibility.R).
We have tried to keep these in sync with the python checks 
automatically run during a pull request, but **have now stopped maintaining the checks in R.
They are kept in the repository merely as an additional resource for teams who work exclusively with R.**
If you discover major discrepancies,
you can nonetheless [let us know](https://github.com/reichlab/covid19-forecast-hub/issues)
and we may address them as time permits.

As an example of using these test from the base of the repository run


```r
source("code/validation/functions_plausibility.R")
```

To check a single file, run


```r
validate_file("data-processed/UMass-MechBayes/2020-04-26-UMass-MechBayes.csv")
```

To check a directory, run


```r
validate_directory("data-processed/UMass-MechBayes/")
```

Any "ERROR"s will result in a failed pull request. 
"Warning"s and "Message"s are informational, but may help prevent unwanted 
or incomplete forecasts from getting pushed to the repository.

In addition to purely technical sanity checks, three plausibility checks 
are performed:

- avoid quantile crossing: quantiles should be non-decreasing, e.g. it 
does not make sense to have a median of 500, but a 75% quantile of 400 in
the same forecast.
- avoid temporal inconsistencies: quantiles of *cumulative* forecasts should
be non-decreasing over time. It does not make sense to predict a median
of 500 one week ahead and a median of 400 two weeks ahead (for the same
cumulative target and if both forecasts are issued at the same time).
- avoid inconsistencies between incidence and cumulative forecasts:
quantiles of cumulative deaths should not be below those of incident 
deaths for the same forecast horizon (as incident deaths are a subset of 
cumulative deaths).

If inconsistencies are found here, the list returned by `validate_file`
contains a table pointing you to the respective parts of your file which
caused the problem.
