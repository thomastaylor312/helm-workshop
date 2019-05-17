# Helm Workshop: KubeCon Barcelona 2019

The purpose of this workshop is to learn how to use Helm and build Helm charts

## Workshop Format

This workshop is split into numbered sections outlined below:
1. [Getting Started](01-getting-started/)
2. [Installing Charts](02-installing-charts)
3. [Charts](03-charts/)
4. [Subcharts](04-subcharts/)
5. [Templating](05-templating/)

## Goals

We have two goals for this workshop:

1. Learn how to install and manage charts
2. Create your own Helm Chart using Docker's popular [voting app
   demo](https://github.com/dockersamples/example-voting-app) and install it in
   a Kubernetes cluster.

We will start with a simple chart, and then add from there.

Our application will have five parts:

1. A front-end for users to vote
1. A back-end that tallies votes
1. An admin interface to see the results
1. A Redis cache
1. A PostgreSQL database

### Quick Reference

In this guide, we will be building a chart based on a sample voting app. Here
are the images you will need:

- `redis:alpine` - The Redis server for a work queue
- `postgres:9.4` - The persistent data storage
- `dockersamples/examplevotingapp_result:before` - The admin viewer
- `dockersamples/examplevotingapp_vote:before` - The voting frontend
- `dockersamples/examplevotingapp_worker` - The queue worker
