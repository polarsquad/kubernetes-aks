# Exposing the app

## Services

When we deployed the very first service in our excercises with the `first_manifest.yaml` file, it included a service of type LoadBalancer. Use the skills you have accumulated and the Azure portal to find out what kind of resources have been configured in Azure when creating this service in Kubernetes. 

What about the service we created without specifying a type. Can you find any associated Azure resources.

## Ingresses

Next let's create an ingress for your service. Let's keep working on the `first_manifest.yaml` file and edit it to have a ClusterIP Type service and an Application Gateway ingress. Look at the previous exercises to determine how to configure a basic service. The Application Gateway ingress can be created as follows:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-app
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - host: <your-namespace>.aks.polarsquad.training
    http:
      paths:
      - path: /
        backend:
          service:
            name: hello-world-app
            port:
              number: 80
        pathType: Exact
```

Your nginx app would then be reachable through the url `http://<your-namespace>.aks.polarsquad.training`

Now that you have configured an ingress for your application, find the application gateway in the Azure portal and find how creating the ingress resource configured the application gateway.