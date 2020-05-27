# ReplicationController

A ReplicationController ensures that a specified number of pod replicas are running at any one time. In other words, a ReplicationController makes sure that a pod or a homogeneous set of pods is always up and available.


## How a ReplicationController Works

If there are too many pods, the ReplicationController terminates the extra pods. If there are too few, the ReplicationController starts more pods. Unlike manually created pods, the pods maintained by a ReplicationController are automatically replaced if they fail, are deleted, or are terminated. For example, your pods are re-created on a node after disruptive maintenance such as a kernel upgrade. For this reason, you should use a ReplicationController even if your application requires only a single pod. A ReplicationController is similar to a process supervisor, but instead of supervising individual processes on a single node, the ReplicationController supervises multiple pods across multiple nodes.

## Running an example ReplicationController

```
# cd k8s-terraform-cgi-25May2020/03-K8s/03-Replication-controller

# cat helloworld-repl-controller.yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: helloworld-controller
spec:
  replicas: 5
  selector:
    app: rc-scaling-pods
  template:
    metadata:
      labels:
        app: rc-scaling-pods
    spec:
      containers:
      - name: rc-scaling-pods
        image: amitvashist7/k8s-tiny-web
        ports:
        - name: nodejs-port
          containerPort: 80

```

# Run the helloworld RC with following command:

```
kubectl apply -f helloworld-repl-controller.yml
```

# Check on the status of the ReplicationController using this command:

```

```

```

```

# Common usage patterns

## Rescheduling
As mentioned above, whether you have 1 pod you want to keep running, or 1000, a ReplicationController will ensure that the specified number of pods exists, even in the event of node failure or pod termination (for example, due to an action by another control agent).

## Scaling
The ReplicationController makes it easy to scale the number of replicas up or down, either manually or by an auto-scaling control agent, by simply updating the replicas field.

## Rolling updates
The ReplicationController is designed to facilitate rolling updates to a service by replacing pods one-by-one.


## Let's Scaleout the Pods from 5 to 10.
```

```

```

```

## Let's ScaleIn the Pods from 10 to 3.
```

```

```

```
