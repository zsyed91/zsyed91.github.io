---
layout: post
title: Running rails db:migrate in kubernetes 
summary: How to run db:migrate during dev work in a kubernetes pod 
toc: true
---

## Overview

I am working on a dev project that is written in rails but deployed within kubernetes. While doing local development, I am
running the cluster using `skaffold dev`. This allows me to make changes locally and have it auto-sync to the running container
without having to rebuild the image and push it anywhere. One issue with this approach is that in order to run database migrations
locally, creating a deployment or job is a little bit heavy handed. Instead, we can simply connect to the running pod and run our
`db:migrate` command there.

## Connecting to a kubernetes pod

In order to run `rails db:migrate` we need to connect to a running pod. In order to determine which pod we need to connect to,
we can use `kubectl` to get the name of the pod that is running the rails server. In my project, I have named that pod simply `server-deployment`.

```
> kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
server-deployment-6886cb479c-tt4mf   1/1     Running   36         73d
```

With the above, we can grab the full pod name and run the following command which will connect to the running pod, run the command, and exit.

```
kubectl exec server-deployment-6886cb479c-tt4mf -- /bin/bash -c "rails db:migrate"
```

However, we can do this in one single step instead of two. The single line command looks like this:

```
kubectl exec $(kubectl get pods | grep server | sed 's/ .*//') -- /bin/bash -c "rails db:migrate"
```

The command above is doing the following:

- The sub-command `$(kubectl ...)` is getting all the pods, grepping for our server pod name, and then stripping everything but the first word that contains our full pod name
- This is then substituted into the `kubectl exec <our_pod_name>`
- Then finally, the `/bin/bash -c "rails db:migrate"` runs a one off command and auto-exits (we are not opening an interative connection
- Profit!


### TLDR

To run `rails db:migrate` during our development flow, we can connect to the running rails pod and run that command via:

```
kubectl exec $(kubectl get pods | grep server | sed 's/ .*//') -- /bin/bash -c "rails db:migrate"
```
