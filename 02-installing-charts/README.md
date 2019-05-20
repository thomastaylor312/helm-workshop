# Installing and Configuring Charts

## Chart Repositories
Helm Charts are often stored in public or private repositories. Helm can fetch specific versions of charts from these repositories and install them on the cluster.

There are two curated public repositories managed by a set of Helm maintainers: [stable](https://github.com/helm/charts/tree/master/stable) and [incubator](https://github.com/helm/charts/tree/master/incubator). These Charts are well maintained and generally follow the best practices for writing Charts.

### Searching for Charts
By default, Helm will search for charts in the stable repo.

```console
$ helm repo list
```

Optional: you can add other repos, such as the incubator repo:

```console
$ helm repo add incubator https://storage.googleapis.com/kubernetes-charts-incubator
```

You can list all of the available charts by running:

```console
$ helm search
NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                                 
stable/acs-engine-autoscaler            2.2.0           2.1.1                           Scales worker nodes within agent pools                      
stable/aerospike                        0.1.7           v3.14.1.2                       A Helm chart for Aerospike in Kubernetes                    
stable/anchore-engine                   0.2.6           0.2.4                           Anchore container analysis and policy evaluation engine s...
stable/apm-server                       0.1.0           6.2.4                           The server receives data from the Elastic APM agents and ...
...
```

You can see from the output a brief description and then two different versions.
The Chart Version is the version of the package itself and the App Version is
the version of the software it is installing.

You can also search for specific charts using keywords or names of the
applications like so:

```console
$ helm search prometheus
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION                                                 
stable/prometheus                       7.4.1           2.5.0           Prometheus is a monitoring system and time series database. 
stable/prometheus-adapter               v0.2.0          v0.2.1          A Helm chart for k8s prometheus adapter                     
stable/prometheus-blackbox-exporter     0.2.0           0.12.0          Prometheus Blackbox Exporter                                
stable/prometheus-cloudwatch-exporter   0.2.1           0.5.0           A Helm chart for prometheus cloudwatch-exporter             
[...]
stable/weave-cloud                      0.3.0           1.1.0           Weave Cloud is a add-on to Kubernetes which provides Cont...
stable/kube-state-metrics               0.11.0          1.4.0           Install kube-state-metrics to generate and expose cluster...
stable/mariadb                          5.2.3           10.1.37         Fast, reliable, scalable, and easy to use open-source rel...
```

## Install a Chart

Your Kubernetes cluster starts with no helm charts installed:

```console
$ helm list
```

Use the `install` command to install a chart from a repository.

```console
$ helm install stable/wordpress
NAME:   hardy-indri
LAST DEPLOYED: Wed May 15 10:25:54 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                       DATA  AGE
hardy-indri-mariadb        1     0s
hardy-indri-mariadb-tests  1     0s

==> v1/Deployment
NAME                   READY  UP-TO-DATE  AVAILABLE  AGE
hardy-indri-wordpress  0/1    1           0          0s

==> v1/PersistentVolumeClaim
NAME                   STATUS  VOLUME                                    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
hardy-indri-wordpress  Bound   pvc-1d322677-772e-11e9-b448-025000000001  10Gi      RWO           hostpath      0s

==> v1/Pod(related)
NAME                                    READY  STATUS             RESTARTS  AGE
hardy-indri-mariadb-0                   0/1    Pending            0         0s
hardy-indri-wordpress-686b47bf4f-6ddc6  0/1    ContainerCreating  0         0s

==> v1/Secret
NAME                   TYPE    DATA  AGE
hardy-indri-mariadb    Opaque  2     0s
hardy-indri-wordpress  Opaque  1     0s

==> v1/Service
NAME                   TYPE          CLUSTER-IP     EXTERNAL-IP  PORT(S)                     AGE
hardy-indri-mariadb    ClusterIP     10.100.14.7    <none>       3306/TCP                    0s
hardy-indri-wordpress  LoadBalancer  10.110.100.88  <pending>    80:32672/TCP,443:31390/TCP  0s

==> v1beta1/StatefulSet
NAME                 READY  AGE
hardy-indri-mariadb  0/1    0s


NOTES:
1. Get the WordPress URL, using your release name instead of `hardy-indri`:

  NOTE: It may take several minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w _release-name_-wordpress' - the EXTERNAL-IP will change from <pending> to an IP address.

  export SERVICE_IP=$(kubectl get svc --namespace default hardy-indri-wordpress --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo "WordPress URL: http://$SERVICE_IP/"
  echo "WordPress Admin URL: http://$SERVICE_IP/admin"

2. Login with the following credentials to see your blog

  echo Username: user
  echo Password: $(kubectl get secret --namespace default hardy-indri-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
```

This creates a "Release," which is the unique combination of your configuration
applied to the chart. You can name the release or Helm will automatically
generate one for you. The output shows all of the resources that were created
and some getting started steps for this specific chart. To see the status of all
resources again, you can run `helm status <release_name>`. You'll also see that
the output from installing the chart includes some steps specific for this
chart. Follow those steps to take a look at your new wordpress server!

### So what did this all do?!
That output shows a lot of things, so what did the chart create/install?

When you ran the `helm install` command, you created a Release. You can view
that release by running

```console
$ helm get <release_name>
```

This will output all of the rendered Kubernetes YAML files, the time when it was
released, which chart it was using, and the computed configuration values. 

As mentioned before, you can get all of the Kubernetes resources the release
created by running:

```console
$ helm status <release_name>
RESOURCES:
==> v1/ConfigMap
NAME                       DATA  AGE
hardy-indri-mariadb        1     18m
hardy-indri-mariadb-tests  1     18m

==> v1/Deployment
NAME                   READY  UP-TO-DATE  AVAILABLE  AGE
hardy-indri-wordpress  1/1    1           1          18m

==> v1/PersistentVolumeClaim
NAME                   STATUS  VOLUME                                    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
hardy-indri-wordpress  Bound   pvc-1d322677-772e-11e9-b448-025000000001  10Gi      RWO           hostpath      18m

==> v1/Pod(related)
NAME                                    READY  STATUS   RESTARTS  AGE
hardy-indri-mariadb-0                   1/1    Running  0         18m
hardy-indri-wordpress-686b47bf4f-6ddc6  1/1    Running  0         18m

==> v1/Secret
NAME                   TYPE    DATA  AGE
hardy-indri-mariadb    Opaque  2     18m
hardy-indri-wordpress  Opaque  1     18m

==> v1/Service
NAME                   TYPE          CLUSTER-IP     EXTERNAL-IP  PORT(S)                     AGE
hardy-indri-mariadb    ClusterIP     10.100.14.7    <none>       3306/TCP                    18m
hardy-indri-wordpress  LoadBalancer  10.110.100.88  localhost    80:32672/TCP,443:31390/TCP  18m

==> v1beta1/StatefulSet
NAME                 READY  AGE
hardy-indri-mariadb  1/1    18m
```

### Deleting a release
If you no longer need a release, you can delete it. Deleting a release will
remove all Kubernetes resources that it created. Helm will retain the history so
you can restore a deleted release if needed, by performing a rollback to the desired version.

```console
$ helm delete <release-name>
release "<release-name>" deleted
$ helm history <release-name>
REVISION	UPDATED                 	STATUS    	CHART          	DESCRIPTION      
1       	Fri May 17 17:00:41 2019	SUPERSEDED	wordpress-3.3.0	Install complete 
2       	Fri May 17 17:30:22 2019	SUPERSEDED	wordpress-3.3.0	Upgrade complete 
3       	Fri May 17 17:36:56 2019	DELETED   	wordpress-3.3.0	Deletion complete
```

### Configuring a chart
When you installed the chart previously, you did not provide any additional
configuration, so the chart used its defaults. Now let's try providing some
basic configuration to the chart.

All of the charts in the stable repository have their configuration values
documented. You can look at the [Wordpress chart documentation](https://github.com/helm/charts/tree/master/stable/wordpress) or can run
`helm inspect stable/wordpress` to view all metadata and available parameters
for the Chart.

For this step, you'll want to change the name of the blog, set your first and
last name, and change the email address. To do this, first create a file called
`extra-values.yaml`. To set a value, you'll need to find the value name and add
it to this values file you created. An example of this with changing the email
address is below:

```yaml
wordpressEmail: mycoolemail@example.com
```

Look in the [Wordpress chart documentation](https://github.com/helm/charts/tree/master/stable/wordpress) to find the names of the values to change your
first and last name and the name of the blog and add those to your
`extra-values.yaml` file.

Now we'll need to install wordpress with this configuration, but while we are at
it, let's set a different password

```console
$ helm install stable/wordpress -f extra-values.yaml --set wordpressPassword=supersecure
```

The `-f` flag tells Helm to use the values in that file to override the defaults
set in the chart. The `--set` flag allows you to override any value when the
command runs. Values files are often used for providing different configuration
values per environment, and the set flag is often used to set secret values at
install time.

Follow the getting started steps that print out again and observe how the values
you provided were used to set up the blog. You should be able to log in with the
password you provided and see the name of the blog you chose

If you do another `helm get <release_name>`, you'll see that there are now
values in the "USER SUPPLIED VALUES" section. If you find those same value keys
in the "COMPUTED VALUES" section, you'll see that they are set there as well.

### Upgrading and rolling back a release
In this step, you will upgrade a release using some broken settings and then
roll it back to a working version

Run the following command to upgrade your release:

```console
$ helm upgrade <release_name> stable/wordpress --reuse-values --set mariadb.enabled=false
```

Note that with the upgrade command, you need to provide the release name and the
chart to use. The `--reuse-values` flag is a handy shortcut that makes it so you
don't have to pass the values file again with `-f` as it will reuse all of the
values set in the current release.

So what did we do here? We set the database to false, which removed that pod. Without a database, our wordpress will not be healthy.

If you run `helm status <release_name>` or `kubectl get pods` you'll see that
the new wordpress pod isn't in a ready state (and no amount of waiting will move it into a ready state):

```console
NAME                                        READY  STATUS                      RESTARTS  AGE
brown-greyhound-wordpress-6f8b897899-nvplh  0/1    Running                     0         2m52s
```

Luckily for us, we have the `helm rollback` command. You can rollback to any
revision of a release. To see all the revisions of a release, you can run:

```console
$ helm history <release_name>
REVISION    UPDATED                     STATUS      CHART           DESCRIPTION     
1           Wed May 15 11:03:36 2019    SUPERSEDED  wordpress-5.9.3 Install complete
2           Wed May 15 11:13:04 2019    DEPLOYED    wordpress-5.9.3 Upgrade complete
```

There is also a shortcut that we will use here to rollback to the last release:

```console
$ helm rollback <release_name> 0
Rollback was a success.
```

If you run `helm history` again, you'll see that it rolled back to the previous
release:

```console
$ helm history <release_name>
REVISION    UPDATED                     STATUS      CHART           DESCRIPTION     
1           Wed May 15 11:03:36 2019    SUPERSEDED  wordpress-5.9.3 Install complete
2           Wed May 15 11:13:04 2019    SUPERSEDED  wordpress-5.9.3 Upgrade complete
3           Wed May 15 11:19:25 2019    DEPLOYED    wordpress-5.9.3 Rollback to 1 
```

Then, if you run `helm status <release_name>` or `kubectl get pods`, you'll see
that the pod is running again and this time it's in a ready state:

```console
==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
brown-greyhound-mariadb-0                   1/1    Running  0         102s
brown-greyhound-wordpress-77479976b8-s82jg  1/1    Running  1         17m
```

You can also use `rollback` to restore a deleted release. To undo a delete, you need the release name and the desired version to redeploy:

```console
$ helm rollback <release-name> <revision-number>
Rollback was a success.

```

After deletion, re-creation will take a little time, so you can watch with `kubectl get all` until you see all resources healthy again.


## Conclusion
At this point, you have practiced how to install a chart, how to upgrade, delete, and rollback a
release, and how to customize release configuration. You can delete any releases and run through these exercises again if desired!

Up next: [Charts](../03-charts/).
