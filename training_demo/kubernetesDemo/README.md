# Kube demo

## Show kube context

`kubectl config get-contexts`

## Apply manifest

`kubectl apply -f manifest.yaml`

## View results

`watch kubectl get pods`

`kubectl get all`

`kubectl get ing`

## Test the app

`curl <your namespace here>.k8s.polarsquad.training`

## Look at logs

`kubectl logs -l app=hello-world-app`

## Scaling manually

`kubectl scale deployment hello-world-app --replicas=3`

## Rollout status

`kubectl rollout status deployment hello-world-app`
`kubectl rollout history deployment hello-world-app`

## Delete deployment

`kubectl delete -f manifest.yaml`
