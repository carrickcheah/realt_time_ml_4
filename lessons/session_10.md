# Session 10

### Table of Contents

- [1. Goals](#1-goals)
- [2. Questions and answers](#2-questions-and-answers)
- [3. Nuggets of wisdom](#3-nuggets-of-wisdom)
- [4. Video recordings and slides](#4-video-recordings-and-slides)
- [5. Homework](#5-homework)

## 1. Goals

- [x] Wrap up the training pipeline
    - [x] Add model validation step
    - [x] Push model to the registry only if it's better than the baseline
    - [x] Dockerize
    - [ ] Deploy as Job
    - [ ] Deploy as CronJob

Pending
- [ ] Add MLFLOW env variables to the training-pipeline Job
- [ ] Improve logging in model validation step in `train.py`
- [ ] Turn Job into CronJob
- [ ] Adjust `deploy.sh` to deploy either with `kustomize build` or just a single `kubectl apply`


## 2. Questions and answers


## 3. Nuggets of wisdom

### Kubernetes jargon

- Pod
- Deployment
- Service
- Job
- CronJob
- StatefuSet (haven't covered in the course)

### What is helm?
Helm is a package manager for Kubernetes.
You don't need to write one from scratch, you can start from a chart template.

Here is an example from the podinfo project:
https://github.com/stefanprodan/podinfo/tree/master/charts/podinfo

### Service port vs Container port

For example, MLflow runs as a pod that exposes port 5000.

The mlflow service exposes port 80 to outside traffic, and maps it to the pod port 5000.


## 4. Video recordings and slides

- [Video recordings](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157605588)

- [Slides](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157605588/posts/2187490001)


## 5. Homework

- Adjust the `train.py` so that instead of using a single candidate to train (`n_model_candidates = 1`) we can support
fitting hyperparameters for N candidates models, and then choosing the best out of this N candidates models trained with hyperparameter tuning.

- Add data drift detection to the `train.py` script using Evidently.

- Add model drift detection to the `train.py` script using Evidently.
    
    https://docs.evidentlyai.com/metrics/preset_regression#report-customization
    ```python
    report = Report([
        RegressionPreset(),
    ],
    include_tests=True)

    my_eval = report.run(current, ref)
    ```
    

- Instead of having 3 different dockerfiels for `trades`, `candles`, or `training-pipeline` use docker build arguments to build the image.
    - https://discord.com/channels/1003641277021175848/1239859045448552448/1369417997747486830

- Write a helm chart for one of our services.


