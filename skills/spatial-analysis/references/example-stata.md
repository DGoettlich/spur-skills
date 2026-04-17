# Stata Example

Use this file to implement the Becker, Boll, and Voth (2026) workflow in Stata.

The `spur` package handles the tests, transformations, and half-life step. The `scpc` package handles inference.

The main Stata commands are:

- `spurtest`
- `spurtransform`
- `scpc`

Use `spurhalflife` only if persistence itself is part of the question.

## Install

If you want to run this workflow in Stata, install the required packages first.

```stata
ssc install moremata, replace
net install spur, replace from(https://raw.githubusercontent.com/pdavidboll/spur/main/)

cap ado uninstall scpc
net install scpc, from("https://raw.githubusercontent.com/ukmueller/SCPC/master/src")
```

`moremata` is required by the Stata `spur` package.

## Setup

This example assumes your data are already loaded in Stata and include:

- `am` as the outcome
- `gini` and `fracblack` as regressors
- `lat` and `lon` as coordinates

Both `spur` and `scpc` expect the coordinate variables to be named `s_1` and `s_2`. With `latlong`, `s_1` is latitude and `s_2` is longitude.

```stata
rename lat s_1
rename lon s_2

set seed 42
```

## Step 1: Test The Dependent Variable

```stata
spurtest i0 am, nrep(100000) latlong
spurtest i1 am, nrep(100000) latlong
```

Use the decision rule from `SKILL.md` to decide which branch to follow next.

## Step 2A: Levels Branch

Use this branch only if the decision rule in `SKILL.md` tells you to stay in levels.

```stata
reg am gini fracblack, r
scpc, latlong
```

## Step 2B: Transformed Branch

Use this branch if the decision rule in `SKILL.md` tells you to transform the regression.

```stata
spurtransform am gini fracblack, prefix(h_) transformation(lbmgls) latlong replace

reg h_am h_gini h_fracblack, r
scpc, latlong
```

## Optional: Half-Life

Use this only when persistence itself is part of the question.

```stata
spurhalflife am, nrep(100000) level(95) latlong
```

## Practical Notes

- If your coordinates are already named `s_1` and `s_2`, you do not need to rename them.
- If you work with Euclidean coordinates instead of latitude and longitude, keep the `s_*` naming but drop the `latlong` option.
- `scpc` is a postestimation command, so run it right after the regression you want inference for.
- `spurtransform` defaults to `lbmgls`; it is written out here so the transformation step is explicit.
- `replace` makes it easier to rerun the code by overwriting existing `h_*` variables.
- `nrep(100000)` matches the package default and is a good choice for real analysis.
