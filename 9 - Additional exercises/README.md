# Kubernetes exercises
This repository contains some additional Kubernetes exercises, some of these manifest files are broken in some way, some are just missing configuration data or other important pieces.

You can use tools like kubeconform to catch some of the errors, but others will just produce deployments that are broken in some way, but can still be applied to your cluster and start running, but the connections between the resources don't work properly.

You could probably use tools like 'diff' to compare to the other exercises and catch the errors, but that wouldn't help with the learning process, would it?

## Module 1 - Running Individual Containers

You'll find two manifest files in the directory. First one has broken syntax, so fix it. The second one is missing some information. Fill out the missing bits.

## Module 2 - Running a Fleet of Containers

One deployment, many problems. You can start with tools like kubeval, but there are multiple things going wrong here.

## Module 3 - Configuring Applications

The intention is to mount the nginx.conf to an nginx deployment.

Something's off in the configuration maybe? Try to figure out where the problem is. 

## Module 4 - Exposing Apps using Ingress

This exercise has three files that need some work. You'll need to specify the hostname for the ingress like in the earlier  exercises before you start making any additional changes. Make sure also that the ingress is configured right for Azure.
