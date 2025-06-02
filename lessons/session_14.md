# Session 14

## Questions

Quick one @Pau; Hypothetically speaking, I have a dataset of EV energy consumption from EV charging stations and I'll like to build a time-series model based on it. However, the end-time column is inaccurate due to some reasons. What do you suggest i do?

Fix data problems as upstream as you can.
Bound the uncertainity. Are you off 20% or 100%.


Lachezar: Could you please share what is the difference between this cohort's LLM to previous one.
Sorry, but I connected 2 minutes later you might have shade light on this.

## Goals

- [x] Wrap up the Rust API

    - [x] Use Rust modules to split the code into separate files
    - [x] Set the output type of the `/predictions` endpoint to JSON. 
    - [x] Extract hardcoded values as environmnet variables
    - [x] Extract PoolPg as a "global" variable, instead of recreating it for every incoming request.
    - [x] Write a materialized view in RisingWave that has the latest prediction for each pair. So our REST API just "looks up" this table.
    - [x] Add some logging.
    - [x] Write a Dockerfile.
    - [x] Write the YAML manifests, `Deployment`, `Service`
    - [x] Extract service config into a "global" object that can be used in differewnt parts of the code, without
    - [x] Deploy our REST API to Kubernetes

## Homework

- Create a flamegraph for this REST API, and compare it to the equivalent code written with FastAPI
    https://www.brendangregg.com/flamegraphs.html

- Rootless image for Rust to get a minimal Docker image for the predicton-api service
    [FROM scratch](https://hub.docker.com/_/scratch)