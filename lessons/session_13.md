# Session 13 - Office hours

### Table of Contents

- [1. Goals](#1-goals)
- [2. Questions and answers](#2-questions-and-answers)
- [3. Nuggets of wisdom](#3-nuggets-of-wisdom)
- [4. Video recordings and slides](#4-video-recordings-and-slides)
- [5. Homework](#5-homework)

## 1. Goals for today
- [ ] Answer as many questions as possible
- [ ] Wrap up the Rust API

    - [x] Use Rust modules to split the code into separate files
    - [x] Set the output type of the `/predictions` endpoint to JSON. 
    - [x] Extract hardcoded values as environmnet variables
    - [ ] Add some logging.
    - [ ] Write a materialized view in RisingWave that has the latest prediction for each pair. So our REST API just "looks up" this table.
    - [ ] Write a Dockerfile
    - [ ] Write the YAML manifests, `Deployment`, `Service`
    - [ ] Extract PoolPg as a "global" variable, instead of recreating it for every incoming request.
    - [ ] Extract service config into a "global" object that can be used in differewnt parts of the code, without
    manually reading environment variables with `std::env`.

## 2. Questions and answers

### Question 1. How to deploy the services to Kubernetes? (Ramon)

Deploying a service to Kubernetes means doing 2 things:

- Building a Docker image for that service and pushing it to a container registry where you cluster
can load in the next step.

- Apply the Kubernetes YAML manifests for that service using `kubectl apply`
For example:
    - for trades we only had one `Deployment`.
    - for the backfil-pipeline we had several YAML files, including 3 `Job`s anb 1 `ConfigMap`
    - for the `prediction-api` (which we haven't completed yet) we will have 1 `Deployment` and 1 `Service`


#### Method 1 -> Apply a single YAML file

This is what we did in the first 2 when we had a single `Deployment` for each service/

```sh
kubectl apply -f deployments/dev/trades/trades-d.yaml
```


#### Method 2 -> Use kustomize to build the manifests and then one kubectl apply

```sh
kustomize build -f PATH_TO_THE_KUSTOMIZATION_YAML_FILE | kubectl apply -f 
```

#### Method 3 -> GitOps

A tool like Flux synchronizes code changes (meaning YAML file changes) and docker image changes with
your Kubernetes cluster. So you get automatic deployments.


### Question 2. Is there a way to edit a file inside the container without external mounts? (Peter)

Yes, you can. But is not recommended.

Marius will share an example of VSCode container with external volume that persists changes.

### Question 3. In a real project, would you deploy to Kubernetes manually first and then with GitOps? (Lachezar)

In a real project you go with GitOps from day 1.

Popular tools for GitOps
- [FluxCD](https://fluxcd.io/)
Most simple and most debuggable one. Acts as YOU inside the cluster. Looks at the manifests from you repo,
and applies them with kubectl apply.

- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
Has nicer UI. OpenShift uses ArgoCD a lot.

What is OpenShift?
It is an enterprise distribution of Kubernetes from RedHat (Marius does not like it)

Flux is simpler to setup, from the terminal.

Nuke cluster? (EING?)
Nuke vs inplace a cluster.
Always move forward.

Nuking a cluster with Flux is fast and safe. Flux CD takes care of applying all manifests you need in the right order, like when you upgrade your cluster.

My recommendation

Go through the get started tutorial from FluxCD documentation
- [Link to the tutorial](https://fluxcd.io/flux/get-started/)

### Question 4. Is kustomize the same solution as Helm for k8s? (Cristiano)

No. They are complementary tools.

- Kustomize for files
- Helm for packages.


### Question 5. What is the `~/.kube/config` file (Ramon)



### Question 6. How to log our services? (Peter)

Easy scenario.
Docker container standalone running. Logs go to standard output.

Kubernetes is you running that container. Logs go to standard output.

EVERYTHING goes by default to STDOUT in container env.


### Question 7. How to organzie projects for several clients?

What is the best practice for managing a single Git project that contains dev, test, and production environments, especially when deploying the same product to multiple clients? Each client connects via VPN to a separate Linux server.

Marius -> One repo per client.

For example, Marius has both Pau and Filip as clients.

Each client gets one repo.

Each client has different environment. For example:
- Pau has 2 environments: dev and prod
- Filip is an enterprise with many more environments: dev, stage, pre-prod, prod, canary-1234

Isolation between clients, both at the code level and (of course) the compute level (aka Kubernetes cluster)

Isolation at the github organization/user level.

Marius working for company X would create a github user called `Marius-X`.


### Question 8. Differences between a dev and a prod cluster? (Herbert)

It should not.

Too save on costs, you will often see dev clusters being a minimized version of your prod cluster. Like we do in this course.

Ideally, you want your dev cluster to mirror 100% production. For example, you want to do load testing on dev cluster that will
match production.

For example, if you want to load test the feature pipeline to support 100x crypto pairs using a tools like

- [k6](https://github.com/grafana/k6) as your load generator
- [Keda](https://keda.sh/docs/2.17/concepts/#architecture) as your autoscaler

you want your dev cluster to support this kind of testing.


**Note**
Kubernetes has a [native autoscaler](https://github.com/kubernetes/autoscaler) that looks as things like CPU consumption memory.
Keda is more advanced, and lets you listen to events like "Kafka lags" (a high lag means your consumers are not consuming messages fast enough, so you need more consumer replicas).

**Video idea**
Show autoscaling in action on a Kubernetes cluster.


### Question 9. Code reuse vs client isolation.

Marius, to isolate, you mentioned you would set a new github username per client. how do you manage code reuse vs client isolation: one core codebase + per-client configuration/isolation (separate deploys, secrets, customization)

Marius' solution:
Template your manifests, code... using templating engines like Jinja, and then render the templates.

One template -> N renders, one per client.

Examples of template engines:
- [Jinja](https://github.com/pallets/jinja)

Value validations
- [Cue](https://github.com/cue-lang/cue)

The whole pipeline for real world templating is:

- Value validation
- Template
- Final artifact


Templating of repos is also possible. For example, [cookie cutter](https://github.com/cookiecutter/cookiecutter).


### Question 10. Is it easy/common to have container to read a config file at start and modify internal sys env based on this ?

A use case - debug/verbose level messages in python code

TLDR: extract this as enviornment variables.

To control the log level you use environment variables, for example for Rust you can use `RUST_LOG` that the program loads to understand
at which level we need to log.

```sh
$ RUST_LOG=info ./main
[2018-11-03T06:09:06Z INFO  default] starting up
```

- [Reloader](https://github.com/stakater/Reloader) listens to changes in config maps and restarts your deployments, services...
Reloader with FluxCD play nicely. You change the config map, commit and push to remote branch. From there, Reloader and FluxCD deploy changes automatically to your
cluster.

What about passing "secrets" to your pods?

- [SOPS](https://github.com/getsops/sops)


### Question 11. What about vaults for secrets? (Shadrack)

- [External Secrets Operator](https://external-secrets.io/latest/) is your Flux for secrets. It helps you ingest secretes from external providers, like AWS, and insert them into your cluster.

### Question 12. 

## 3. Nuggets of wisdom

### Why kind?

Kind is the go-to tool for development and testing purposes.

### Semantic versioning

In companies with 100s of microservices running in produciton you need a unified approach to version and name your services.

```sh
docker buildx build --push \
        --platform linux/amd64 \
        -t ghcr.io/real-world-ml/${image_name}:0.1.5-beta.${BUILD_DATE}  \
        --label org.opencontainers.image.revision=$(git rev-parse HEAD) \
        --label org.opencontainers.image.created=$(date -u +%Y-%m-%dT%H:%M:%SZ) \
        --label org.opencontainers.image.url="https://github.com/Real-World-ML/real-time-ml-system-cohort-4/docker/${image_name}.Dockerfile" \
        --label org.opencontainers.image.title="${image_name}" \
        --label org.opencontainers.image.description="${image_name} Dockerfile" \
        --label org.opencontainers.image.licenses="" \
        --label org.opencontainers.image.source="https://github.com/Real-World-ML/real-time-ml-system-cohort-4" \
        -f docker/${image_name}.Dockerfile .
```

### What engines run containers in real systems?

- ContainerD in Virtual Machines
- Docker Engine/Desktop for laptops


## 4. Video recordings and slides

- [Video recordings](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157649860)

- [Slides](https://www.realworldml.net/products/building-a-real-time-ml-system-together-cohort-4/categories/2157649860/posts/2187663393)


## 5. Homework
