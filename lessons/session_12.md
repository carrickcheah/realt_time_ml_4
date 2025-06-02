# Session 12

### Table of Contents

- [1. Goals](#1-goals)
- [2. Questions and answers](#2-questions-and-answers)
- [3. Nuggets of wisdom](#3-nuggets-of-wisdom)
- [4. Video recordings and slides](#4-video-recordings-and-slides)
- [5. Homework](#5-homework)

## 1. Goals for today

- [x] Deploy the prediction-generator service
- [x] Fix bug where `prediction_horizon_seconds` is hardcoded

## 2. Questions and answers

- 

## 3. Nuggets of wisdom

- It might happen you need to add `git` to the `Dockerfile` to install the `risingwave-py` package.

### Let's Rust a bit

- Install the Rust compiler `rustc` and the package manager `cargo`
    https://www.rust-lang.org/tools/install

- Install the `rust-analyzer` IDE extension

- `cargo new prediction-api`

- `cargo run`

- Run it as a standalone executable: `./target/debug/prediction-api`

- Libraries (aka crates) that can help us build a REST API:
    - [actix-web](https://github.com/actix/actix-web)
    - [axum](https://github.com/tokio-rs/axum)

- `cargo add axum` Crate to build HTTP apis (rest or websocket)

- `cargo add tokio`. Async runtime

- Why? `unwrap()`? A little bit about Rust `Result<SuccessType, ErrorType>`

- Minimal axum server with a `/health` endpoint

- `cargo watch -x run` to run the server with hot reloading.

- Add a `/predictions` endpoint that can read GET parameters

    localhost:3001/predictions?pair=BTC/EUR

## 4. Video recordings and slides

- [Video recordings](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157635312)

- [Slides](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157614663/posts/2187524112)


## 5. Homework

- [ ] 2-stage docker build for training pipeline and for prediction generator
    - The current docker images have around 5GB of size, which is too much.

- [ ] Update the return type of `get_prediction` to `Result<String, sqlx::Error>` and use `?` instead of
`unwrap()`s. This is a nicer way to handle errors.
