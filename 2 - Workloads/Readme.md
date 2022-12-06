# Creating your own workloads

## Pods

Pod is the smallest workload in Kubernetes. Try these things out:
* kubectl explain pod
* kubectl explain pod.metadata
* kubectl explain pod.spec
* kubectl explain pod.spec.containers

Let's start off by creating just one pod. You don't usually create pods by themselves but this might make labels and other identifiers easier to understand.

Create a file called pod.yaml with the following content:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod
  labels:
    app: demo
    style: web-server
spec:
  containers:
  - image: nginxinc/nginx-unprivileged
    name: nginx
    ports:
    - containerPort: 8080
```

This will create a single pod with an nginx image. Run `kubectl apply -f pod.yaml`.

Use kubectl to inspect the pod. Try running `kubectl get pod` and figuring out how to view your app.

Try to figure out what your pod's IP address is, you'll need it soon.

You can also create one-off pods with `kubectl run` like:
```sh
kubectl run --restart=Never --rm -it --image=curlimages/curl call-demo -- http://<your pod IP>:8080
```

Now you can delete your pod. Either destroy it directly with `kubectl delete` or `kubectl delete -f`. 

## Deployments

Let's create an actual deployment then. Copy this into a file called deployment.yaml:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
  labels:
    app: demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo-pod
  template:
    metadata:
      labels:
        app: demo-pod
    spec:
      containers:
      - image: nginxinc/nginx-unprivileged
        name: nginx
        ports:
        - containerPort: 8080
```

Use `kubectl` to apply it. You can use `kubectl get pods` to see that a deployment created pods automatically. 

## New deployment

Now create a new deployment file, but instead use `gcr.io/kubernetes-e2e-test-images/echoserver:2.2` or `e2eteam/echoserver:2.2` from Docker Hub as the image. 

You'll need to edit `spec.selector.matchLabels`and `spec.template.metadata.labels.app`, use a different label and selector than your
existing deployment. Change the container name as well to something else besides nginx. No other changes are necessary since the container uses the same port number.

These labels are the way Kubernetes handles the number of pods that are attached to a deployment. This is also used by services.

## Jobs and Cronjobs

You might want to try your hand at creating a job or cronjob:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```