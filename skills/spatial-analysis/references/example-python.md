# Python Example

Use this file to implement the Becker, Boll, and Voth (2026) workflow in Python.

`spur-python` handles the tests, transformations, and half-life step. `scpc-python` is still under development, so the inference step is not yet available in Python.

The main Python functions are:

- `spurtest()`
- `spurtransform()`
- `spurhalflife()`

This file therefore covers the SPUR side of the workflow in Python and stops before the SCPC inference step.

## Install

If you want to run this example, install `spur-python` first.

```bash
uv venv --python 3.11
uv pip install "git+https://github.com/huggingbeard/spur-python.git@v0.1.0"
```

`scpc-python` is still under development and is not ready for the inference step yet.

## Setup

This example uses the built-in Chetty data that ships with `spur-python`.

```python
from spur import load_chetty_data, spurhalflife, spurtest, spurtransform

df = load_chetty_data()

df = df[~df["state"].isin(["AK", "HI"])][["am", "gini", "fracblack", "lat", "lon"]]
df = df.dropna(subset=["am", "gini", "fracblack", "lat", "lon"]).copy()
```

## Step 1: Test The Dependent Variable

```python
i0_y = spurtest(
    df,
    "i0",
    "am",
    ["lat", "lon"],
    nrep=100000,
    seed=42,
)

i1_y = spurtest(
    df,
    "i1",
    "am",
    ["lat", "lon"],
    nrep=100000,
    seed=42,
)

print(i0_y.summary())
print(i1_y.summary())
```

Use the decision rule from `SKILL.md` to decide which branch to follow next.

## Step 2A: Levels Branch

If the decision rule in `SKILL.md` tells you to stay in levels, there is nothing to transform in Python.

The workflow stops here for now, because `scpc-python` is still under development and the inference step is not yet available in Python.

## Step 2B: Transformed Branch

If the decision rule in `SKILL.md` tells you to transform the regression, transform the dependent and independent variables together.

```python
transformed = spurtransform(
    df,
    ["am", "gini", "fracblack"],
    ["lat", "lon"],
    method="lbmgls",
    prefix="h_",
)

transformed[["h_am", "h_gini", "h_fracblack"]].head()
```

At this point the transformed variables are ready, but the inference step is still not available in Python because `scpc-python` is under development.

## Optional: Half-Life

Use this only when persistence itself is part of the question.

```python
hl = spurhalflife(
    df,
    "am",
    ["lat", "lon"],
    nrep=100000,
    seed=42,
)

print(hl.summary())
```

## Practical Notes

- `coord_cols` should be passed as `["lat", "lon"]` when you work with latitude and longitude.
- `spurtransform()` defaults to `method="lbmgls"` and `prefix="d_"`, so this example only sets `prefix="h_"` explicitly to match the R and Stata references.
- `nrep=100000` matches the package default and is a good choice for real analysis.
- `scpc-python` is still under development, so this reference does not show a Python inference step.
- Do not present ordinary Python regression standard errors as a substitute for SCPC inference.
