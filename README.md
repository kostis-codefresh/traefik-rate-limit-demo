# Rate limit your Kubernetes applications with Codefresh and Traefik

[Rate Limiting](https://en.wikipedia.org/wiki/Rate_limiting) is a technique for controlling the rate of requests to your application.

It can save you from denial of service attacks or resource starvation problems.

[Traefik](https://traefik.io/) is a proxy that supports rate limiting out of the box
and can also work as a Kubernetes ingress.

## How to build and run container

Run

 *  `docker build . -t my-app` to create a container image 
 *  `docker run -p 8080:8080 my-app` to run it

 then visit http://localhost:8080 in your browser

You can find prebuilt images at [https://hub.docker.com/r/kostiscodefresh/traefik-demo-app](https://hub.docker.com/r/kostiscodefresh/traefik-demo-app)

## Deploying to Kubernetes with Codefresh

There is a Codefresh pipeline for 2 environments (QA and Prod)
that you can use at [codefresh.yml](codefresh.yml)

## Deploying with rate limits

First install traefik in your cluster

```
helm repo add hub https://helm.traefik.io/hub
kubectl create ns traefik
helm install --namespace=traefik traefik traefik/traefik
```

Create the two namespaces

```
kubectl create ns qa
kubectl create ns prod
```

Deploy using Codefresh or manually with

kubectl apply -f manifests-qa/* -n qa
kubectl apply -f manifests-prod/* -n prod

## Test rate limiting

You can use several testing tools such as

* [ab](https://httpd.apache.org/docs/current/programs/ab.html)
* [siege](https://github.com/JoeDog/siege)
* [hey](https://github.com/rakyll/hey)
* [fortio](https://github.com/fortio/fortio) 

Example with siege

```
siege -c 10 -r 20  http://kostis-eu.sales-dev.codefresh.io/prod
siege -c 10 -r 20  http://kostis-eu.sales-dev.codefresh.io/qa
```

In the second run you should see responses with HTTP code 429 (too many requests).
