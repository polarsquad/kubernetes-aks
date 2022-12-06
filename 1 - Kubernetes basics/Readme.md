# Basics and connecting

## Connecting to training cluster

To connect to the training cluster, run `az account set --subscription <subscription-id>`. You have received the subscription_id via email.
Next we need to fetch the sign-in information for the AKS cluster and merge the info to the kube config file. To do this, run: `az aks get-credentials --resource-group <rg-name> --name <aks-cluster-name>`. This Uses `~/.kube/config`, the default location for the Kubernetes configuration file. Specify a different location for your Kubernetes configuration file using `--file` argument. Replace resource group name and AKS cluster name again with the values you have received previously.

## Kubectl commands

* get
* describe
* create
* delete
* apply
* explain

More can be found at https://kubernetes.io/docs/reference/kubectl/cheatsheet/

## Kubectl connection, namespaces and contexts

Make sure that your connection is working with `kubectl cluster-info`. 
At this point if you are connecting to the cluster for the first time, it will open a browser window to sign in with your training credentials.

Get a list of contexts by running `kubectl config get-contexts`

You should see your cluster connection and the namespace listed. 

## First app

Copy the first_manifest.yaml from training_demo/kubernetesDemo/ and edit it a bit.

Find the references to Namespaces that the value hello-world in them and change the value to something distinct, for example your first name. YOu should edit three occurances in the file.

Then you can run `kubectl apply -f first_manifest.yaml`. To find out how to reach your demo application, run: `kubectl get service hello-world-app --watch -n <your-namespace>`. You will see a column with a value for EXTERNAL-IP, you can reach your application there.

Get a list of resources the manifest created by running `kubectl get all -n <your-namespace>`. 
We will be interested in the resources running in your namespace, so to set the namespace for the current context run `kubectl config set-context --current --namespace=<your-namespace>`. This way you won't have to input the `-n` argument to all commands.

You can run `kubectl get pods` or `kubectl rollout status deployment/hello-world-app` to see what's happening with the app.

Other resources you might find interesting are:
* namespace
* service

You can use `kubectl get` to see these and many others.

If you want to see the deployment manifest you can use `kubectl get deployment/hello-world-app -o yaml`.