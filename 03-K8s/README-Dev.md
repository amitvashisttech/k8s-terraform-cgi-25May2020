# Deployments

A Deployment provides declarative updates for Pods and ReplicaSets.

You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate. You can define Deployments to create new ReplicaSets, or to remove existing Deployments and adopt all their resources with new Deployments.


# Use Case

The following are typical use cases for Deployments:

- ***Create a Deployment to rollout a ReplicaSet.*** The ReplicaSet creates Pods in the background. Check the status of the rollout to see if it succeeds or not.
- ***Declare the new state of the Pods*** by updating the PodTemplateSpec of the Deployment. A new ReplicaSet is created and the Deployment manages moving the Pods from the old ReplicaSet to the new one at a controlled rate. Each new ReplicaSet updates the revision of the Deployment.
- ***Rollback to an earlier Deployment revision*** if the current state of the Deployment is not stable. Each rollback updates the revision of the Deployment.
- ***Scale up the Deployment to facilitate more load.***
- ***Pause the Deployment*** to apply multiple fixes to its PodTemplateSpec and then resume it to start a new rollout.
- ***Use the status of the Deployment*** as an indicator that a rollout has stuck.
- ***Clean up older ReplicaSets*** that you don’t need anymore.


## Creating a Deployment
```
# cd k8s-terraform-cgi-25May2020/03-K8s/

# cat 04-Deployment/helloworld.yml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: k8s-demo
        image: amitvashist7/k8s-tiny-web
        ports:
        - name: node-port
          containerPort: 80
```

## Create the Deployment by running the following command:
```
kubectl create -f 04-Deployment/helloworld.yml
```
# Check the status of the Deployment using this command:

```
kubectl get deployment
NAME                    DESIRED   CURRENT   READY   AGE
helloworld-controller   5         5         2       8s
```

```
kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
helloworld-controller-27z74   1/1     Running   0          18s
helloworld-controller-f6dgr   1/1     Running   0          18s
helloworld-controller-k2snd   1/1     Running   0          18s
helloworld-controller-m86x5   1/1     Running   0          17s
helloworld-controller-nxnkg   1/1     Running   0          17s

```

```
kubectl describe rc helloworld-controller
Name:         helloworld-controller
Namespace:    default
Selector:     app=rc-scaling-pods
Labels:       app=rc-scaling-pods
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"v1","kind":"ReplicationController","metadata":{"annotations":{},"name":"helloworld-controller","namespace":"default"},"spec... Replicas:     1 current / 1 desired
Pods Status:  1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=rc-scaling-pods
  Containers:
   rc-scaling-pods:
    Image:        amitvashist7/k8s-tiny-web
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age                From                    Message
  ----    ------            ----               ----                    -------
  Normal  SuccessfulCreate  23m                replication-controller  Created pod: helloworld-controller-k2snd
  Normal  SuccessfulCreate  23m                replication-controller  Created pod: helloworld-controller-f6dgr
  Normal  SuccessfulCreate  23m                replication-controller  Created pod: helloworld-controller-27z74
  Normal  SuccessfulCreate  23m                replication-controller  Created pod: helloworld-controller-nxnkg
  Normal  SuccessfulCreate  23m                replication-controller  Created pod: helloworld-controller-m86x5
  Normal  SuccessfulDelete  22m                replication-controller  Deleted pod: helloworld-controller-m86x5
  Normal  SuccessfulDelete  22m                replication-controller  Deleted pod: helloworld-controller-27z74
```

## Let's Scaleout the Pods from 5 to 10.
```
kubectl scale --replicas=10 rc helloworld-controller
```

```
kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
helloworld-controller-22prs   1/1     Running   0          15s
helloworld-controller-5bzp2   1/1     Running   0          15s
helloworld-controller-6jzv8   1/1     Running   0          15s
helloworld-controller-f6dgr   1/1     Running   0          117s
helloworld-controller-fclsh   1/1     Running   0          15s
helloworld-controller-k2snd   1/1     Running   0          117s
helloworld-controller-kt8w4   1/1     Running   0          15s
helloworld-controller-lz7j4   1/1     Running   0          15s
helloworld-controller-nxnkg   1/1     Running   0          116s
helloworld-controller-wj22v   1/1     Running   0          15s
```

## Let's ScaleIn the Pods from 10 to 3.
```
kubectl scale --replicas=3 rc helloworld-controller
```

```
kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
helloworld-controller-f6dgr   1/1     Running   0          84s
helloworld-controller-k2snd   1/1     Running   0          84s
helloworld-controller-nxnkg   1/1     Running   0          83s
```


## Let's try an detele the pod to check deployment in actions
```
kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
helloworld-controller-nxnkg   1/1     Running   0          26m
```
```
kubectl delete pod helloworld-controller-nxnkg
pod "helloworld-controller-nxnkg" deleted
```
```
kubectl get pods
NAME                          READY   STATUS              RESTARTS   AGE
helloworld-controller-wzlcv   0/1     ContainerCreating   0          4s
```
```
kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
helloworld-controller-wzlcv   1/1     Running   0          12s
```

## Rolling Updates


### Expose Deployment 

## Rollback 



## Rollback to revsion version
### Check the version history

### Revsion Version Record

## How to delete the Deployment
```
kubectl get rc
NAME                    DESIRED   CURRENT   READY   AGE
helloworld-controller   1         1         1       29m
```
```
kubectl delete rc helloworld-controller
replicationcontroller "helloworld-controller" deleted
```
