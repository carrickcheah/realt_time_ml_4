# Session 8

### Table of Contents

- [1. Goals](#1-goals)
- [2. Questions and answers](#2-questions-and-answers)
- [3. Video recordings and slides](#3-video-recordings-and-slides)
- [4. Homework](#4-homework)

## 1. Goals

- [x] Install mlflow
    - [x] Get into mlflow and create a user, password pair
    - [x] Create a kubernetes secret using this pair
    - [x] Apply the mlflow manifest
    - [x] Create auth metadata so Mlflow can talk to Postgres
    - [x] Port forwarding so we can run our training script as a standalone Python and communicate with mlflow.

- [ ] Build boilerplate training loop
    - [x] Import mlflow
    - [x] Log mock parameters and metrics.
    - [x] Load data from RisingWave, generate a plot and log it to Mlflow.
    - [x] Add target column
    - [x] Validate the data
        https://greatexpectations.io/blog/ml-ops-great-expectations/
    - [x] Profile the data
    - [x] Split the into train and test sets.
    - [x] Build a simple baseline model and log metrics.
    - [x] Log baseline performance

## 2. Questions and Answers 

- Herbert: But Minio password is pasted in the `risingwave-values.yaml`? What is the proper way
to embed these info in the minio installation?

Check for `existingSecret` in the chart values here:
https://github.com/bitnami/charts/blob/main/bitnami/minio/values.yaml

In general, there are these lines of defense agains leaking sensitive info (like passwords):
1. Adding *-secret.yaml in your .gitignore (we use this one)
2. precommit hook with gitleaks so you don't commit passwords to git by mistake.
3. SOPS
4. External secrets -> https://external-secrets.io/latest/

## 3. Video recordings and slides

- [Video recordings](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157556010)

- [Slides](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157556010/posts/2187268646)


## 4. Homework

- Pandera for data validation. Give it a try, instead of great expectations.\

- Other options for experiment tracking and model registry are
    - CometML
    - Weights and Biases
    - Neptune AI -> which you can self-host (this one is for Marius)
    
- Try using mlflow autolog feature
    https://mlflow.org/docs/latest/tracking/autolog


