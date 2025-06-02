# Session 11

### Table of Contents

- [1. Goals](#1-goals)
- [2. Questions and answers](#2-questions-and-answers)
- [3. Nuggets of wisdom](#3-nuggets-of-wisdom)
- [4. Video recordings and slides](#4-video-recordings-and-slides)
- [5. Homework](#5-homework)

## 1. Goals

Pending
- [x] Add MLFLOW env variables to the training-pipeline Job
- [ ] Improve logging in model validation step in `train.py`
- [x] Turn Job into CronJob
- [ ] Adjust `deploy.sh` to deploy either with `kustomize build` or just a single `kubectl apply`


## 2. Questions and answers


## 3. Nuggets of wisdom

- Don't forget to run the `predictor/query.sql` migration inside a `psql` session.

- The `risingwave-py` from PyPi has bugs and it is very old. 
Instead, use the one from the github repo, latest commit

```sh
uv add "risingwave-py @ git+https://github.com/risingwavelabs/risingwave-py"
```

- The `risingwave-py` from the github repo is not properly packaged, because it depends on `psycopg2` but it is not included in the `pyproject.toml` file.

So, you need to add it manually.

Now, `psycopg2-binary` is an easier way to install it, as you don't need to set up the C compiler and other dependencies.

```sh
uv add "psycopg2-binary==2.9.9"
```

## 4. Video recordings and slides

- [Video recordings](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157614663)

- [Slides](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157614663/posts/2187524112)


## 5. Homework

- Maybe add the current close price to the `predictions` table.

- Do we want to have the `ts_ms` as a primary key or not?

- `prediction_horizon_seconds` is hardcoded in the `predict.py` script.

- Handle better the migrations in `predictor/query.sql`  and `technical_indicators/query.sql`