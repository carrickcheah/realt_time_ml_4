# Session 7

### Table of Contents

- [1. Goals](#1-goals)
- [2. Nuggets of wisdom](#2-nuggets-of-wisdom)
- [3. Video recordings and slides](#3-video-recordings-and-slides)
- [4. Homework](#4-homework)

## 1. Goals

- [x] `scripts/build-and-push.sh trades dev`
- [x] `scripts/deploy.sh trades dev`


    - is_done()
    - from_kraken_websocket_response()

- [ ] Deploy the candles service in backfill mode
    - custom_ts_extractor()

- [ ] Deploy the technical indicators service in backfill mode.

- [ ] Install mlflow



## 2. Nuggets of wisdom

- How to monitor our kind cluster?
    We need to install kube-metrics.

- Added talib as an optional dependency, so I did not have to modify the Dockerfiles for services
that do not need it, like `trades.Dockerfile` and `candles.Dockerfile`.

For `technical-indicators.Dockerfile`, I added the `talib` dependency to the `uv sync` command using the `--extra` flag.

- How to scale the backfill pipeline when we start ingesting 100 of crypto currencies?

    - Increase replicas for the `candles-historical` deployment, and the `technical-indicators-historical` deployment.

    You can set the number of partitions for the `trades` topic to be equal to the number of crypto currencies you are processing. Same for the `candle` topic.
    
    Check the TopicConfig input parameter in the `trades` topic.

    - Before that, we need to increase the memory and CPU for the `risingwave` deployment.



## 3. Video recordings and slides

- [Video recordings](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157539404)

- [Slides](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157539404/posts/2187202910)

## 4. Homework

- Set the docker image version from the `pyproject.toml` file, version field, and not this hardcoded 1.5.0.
- Set up the launch.json so we can debug uv with VSCode like a PRO (Homework for Pau)
- Adjust VSCode font settings (Homework for Pau)
- Increase memory and CPU for RisingWave.
- Change the `trades-historical` from Deployment to Job.
