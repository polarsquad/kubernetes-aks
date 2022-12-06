# Debugging in Kubernetes

## Available in kubectl

* kubectl **events**
* kubectl **logs**
* kubectl **exec**
* kubectl **port-forward**

## Events

Default retention is 1 hour

```shell
$ kubectl get events
```

Just warnings:

```shell
$ kubectl get events --field-selector type=Warning
```

## Logs

Pod / container logs:
```shell
$ kubectl logs -n namespace pod/pod-name <container>
```

## Exec

Getting a shell to running container

```shell
$ kubectl exec -ti -n namespace pod/pod-name <container>
```

Note:
- Same as docker exec

## port-forward

These all connect to the same pod:

```shell
$ kubectl port-forward pod/redis-server-765d459796-258hz 7000:6379
```

```shell
$ kubectl port-forward deployment/redis-server 7000:6379
```

```shell
$ kubectl port-forward service/redis-server 7000:redis
```

Note:
- Works for Services too
- Needs access only to API server, rest of the connection is tunneled

## Things that are often set up incorrectly

When working with Kubernetes, these are the things that are often accidentally set up incorrectly.
If your application never starts, keeps restarting, or you're not able to communicate with your app, it's most likely because of these.

### Docker container / Pod

* Container doesn't start listening to the ports you expected it to.
* [Container doesn't have the permission to run as non-root user](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/#controlling-what-privileges-containers-run-with).

### Deployment

* Pod template labels and selector match labels don't match together.
* Container image name points to the incorrect or non-existing image
* [Liveness or readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) probes point to the wrong endpoint.
* You have not [given enough memory or CPU to the container](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/).
* The Secrets and ConfigMaps the Deployment tries to use don't exist or don't contain the expected key-value pairs.

### Services

* Selector doesn't match the desired Pods.
* Target ports don't match the ports that the Pods use.

### Ingress

* Wrong Ingress class is used. This is often the case when there's multiple Ingress implementations in the cluster.
* The backend service name and/or port don't match the desired Service name and/or port.

## Checklist for troubleshooting

Now that we have a list of tools we can use to debug our applications, we just need to know when to use them.
The more and more you use Kubernetes the better you know when to use which command.
Before you get there, you can get started with this checklist.

1. Can I access my service successfully via Ingress?
    * Open the browser or use curl to contact your app's hostname you set up in Ingress.
    * If you get a 404, check the Ingress resource's `ingressClassName` and `host`.
    * If you get a 503, check the Ingress resource's backend service name and port.
1. Can I access my Service?
    * Run ephemeral container and try to `curl` your service IP
    * Check that Service is mapped correctly to the deployment
1. Has my deployment rolled out correctly?
    * Does `kubectl rollout status deploy myapp` return OK?
    * Do you have Pods ready in `kubectl get deploy myapp` list?
    * Use `kubectl describe` on the failing Pods to investigate what's wrong.
    * Use `kubectl logs` on the failing Pods to investigate if there's any error logs printed when the Pods start.
    * Use `kubectl exec` to view that environment variables and files are in the correct place.
1. Can I access my service successfully via port forward?
    * Open a port forward to your app's service endpoint or Pod e.g. `kubectl port-forward svc/myapp 9999 80`
    * Open the browser or use curl to contact the local port.
    * Check that the ports and selector are configured correctly in the Service resource.
    * Check that the app in the container starts listening the correct port.

A lot of times, application issues appear as errors in the Pods.
When you view a Pod status using `kubectl get` or `kubectl describe`, you might see one of these statuses.

| Status                     | What it means  | What to do about it |
| -------------------------- | -------------- | ------------------- |
| ErrImagePull               | Kubernetes failed to pull a Docker image for the Pod | Check that the Docker image name and tag is correct for your Deployment. |
| Failed | Your Pod failed to start or keep up | Check the logs for Pod to figure out why it fails: `kubectl logs <pod-name>` |
| CrashLoopBackoff           | Your Pod has failed to start after multiple restarts | See above |
| Pending | Kubernetes failed to find a node that has enough resources to run your Pod | Either [lower the resource requirements](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) or create more Kubernetes worker nodes. |
| CreateContainerConfigError | Most commonly, you tried to reference a ConfigMap or a Secret that doesn't exist. | Check the ConfigMaps and Secrets referred in your Deployment, and make sure they exist in Kubernetes. Check for possible typos. |
| ContainerCreating          | The Pod is still starting up. However, there's a few things that might prevent it from starting up properly. For example, if you try to mount volumes (ConfigMaps, Secrets, disks) that don't exist, your Pod will be stuck in this state. | Check that the volumes the Pod tries to mount exist. |