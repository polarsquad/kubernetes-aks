# Module 9 - Helm

## Helm basics

### Repositories
```sh
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm search repo bitnami
```

These commands add a repository to your Helm and do a search without any parameters so basically list all available charts. 

### Installing

You can install charts by running eg.
```sh
$ helm install <my-release> bitnami/<chart>
```

This command will install the <chart> from Bitnami repo as my-release. Just replace bitnami with whatever repository you're working with. If you have a local copy of the chart, you'll need to provide the path to the chart.

You have the option of using the `--dry-run` flag which will just print out what will happen once you run the installation for real.

### Upgrading

You can upgrade your release with
```sh 
$ helm upgrade <my release> <chart-name>
```

You can also use the upgrade command with the `-i` option, this will install the release if it didn't already exist. 

### Setting custom values

You can either edit the values.yaml and make more permanent changes or you can specify your own values when installing/upgrading. You'll just need to add `--set variable=value` or you can create values file that you can then supply with `--values` or `-f` when running Helm.

### Deleting

```sh
$ helm delete <my release>
```
 
## Helm exercise

Now search the Bitnami repo for an app you want to install, something simple like Redis is probably fine and won't crash our poor little training cluster. Remember that you can test things out first with `--dry-run`.

Once you're done, delete the installation.

### Helm chart creation

Let's create a Helm chart to install an application. There's already one in the repository called `demochart`, you can use it if you want to.

You can also create a new template with `helm create [your chart name]` if you want. Helm will automatically create all of the files you'll need and a couple of extra files.

You'll need to figure out what to change. First you'll probably need to find out where you'll specify the image your chart is going to use, then the ingress will need some work. Use `polarsquad/hello-world-app:master` as the image.

Once you've got the hang of it, try using your Go app as the basis. We won't get into subcharts, but you can use an existing Redis in your namespace that you connect the app to.