# Adding the Redis and Postgres Databases

In the previous section we added the first service. Now we need to add our data layer. We could create charts from scratch, but instead, let's use some existing ones.

Try this:

```console
$ helm search redis
NAME                              CHART VERSION APP VERSION   DESCRIPTION    
stable/prometheus-redis-exporter  1.0.2         0.28.0        Prometheus exporter for Redis metrics                       
stable/redis                      7.1.0         4.0.14        Open source, advanced key-value store. It is often referr...
stable/redis-ha                   3.4.2         5.0.3         Highly available Kubernetes implementation of Redis         
stable/sensu                      0.2.3         0.28          Sensu monitoring framework backed by the Redis transport  
```

See how we already have a chart that adds `redis` support? Rather than create our own, let's use that. (Note: If you are feeling extra ambitious, you can write your own Redis chart. But it will take about 45 minutes.)

## Subcharts and `requirements.yaml`

In Helm, a _subchart_ is a regular Helm chart that is a dependency of your chart. By adding it to a special file called `requirements.yaml`, you can let Helm manage that chart for you.

Let's create a `requirements.yaml` file next to the `Chart.yaml` in our chart. Here's what belongs inside:

```yaml
dependencies:
  - name: redis        # from search results above
    version: 7.1.0    # Also from the search results above
    repository: "@stable" # Using the @ symbol allows you to use the name of a repository instead of the URL
```

### Add PostgreSQL on your own

Next, run `helm search postgresql` and repeat the above for Postgres. By the end of that step, you should have two items in your `dependencies` array in `requirements.yaml`

## Download the Dependencies

Run `helm dep up` to have Helm fetch those dependencies for you. When completed, you should be able to see two new directories inside of your chart's `charts` subdirectory.

```console
$ helm dep up
Hang tight while we grab the latest from your chart repositories...
...Unable to get an update from the "local" chart repository (http://127.0.0.1:8879/charts):
  Get http://127.0.0.1:8879/charts/index.yaml: dial tcp 127.0.0.1:8879: connect: connection refused
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈
Saving 2 charts
Downloading redis from repo https://kubernetes-charts.storage.googleapis.com
Downloading postgresql from repo https://kubernetes-charts.storage.googleapis.com
$ ls charts
postgresql-4.0.3.tgz  redis-7.1.0.tgz
```

## Upgrade Your Release

If you now run `helm upgrade voting-app --reuse-values .` you can upgrade your chart and get the new redis and postgres databases installed.

## Tip: Re-installing

If you ever need to reinstall instead of upgrading, use the following:

```
$ helm delete --purge voting-app
```

Then you can re-run a fresh install

Up next: [Templates](../05-templating/).
