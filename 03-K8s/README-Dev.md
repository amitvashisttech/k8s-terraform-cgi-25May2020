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
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment   3/3     3            3           3m3s
```

```
kubectl get replicaset
NAME                               DESIRED   CURRENT   READY   AGE
helloworld-deployment-759fc84489   3         3         3       3m42s

```

```
kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-759fc84489-94f2t   1/1     Running   0          4m8s
helloworld-deployment-759fc84489-ltldf   1/1     Running   0          4m8s
helloworld-deployment-759fc84489-pclvw   1/1     Running   0          4m8s
```

```
kubectl describe deployment helloworld-deployment
Name:                   helloworld-deployment
Namespace:              default
CreationTimestamp:      Wed, 27 May 2020 11:16:12 +0000
Labels:                 app=helloworld
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=helloworld
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 1 max surge
Pod Template:
  Labels:  app=helloworld
  Containers:
   k8s-demo:
    Image:        amitvashist7/k8s-tiny-web
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   helloworld-deployment-759fc84489 (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  4m54s  deployment-controller  Scaled up replica set helloworld-deployment-759fc84489 to 3
```

## Let's Scaleout the Pods from 3 to 10.
```
kubectl scale --replicas=10 deployment helloworld-deployment
```

```
kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-759fc84489-5n7vs   1/1     Running   0          48s
helloworld-deployment-759fc84489-5t8sj   1/1     Running   0          48s
helloworld-deployment-759fc84489-94f2t   1/1     Running   0          7m4s
helloworld-deployment-759fc84489-bzr75   1/1     Running   0          48s
helloworld-deployment-759fc84489-cpmdk   1/1     Running   0          48s
helloworld-deployment-759fc84489-ltldf   1/1     Running   0          7m4s
helloworld-deployment-759fc84489-nsrh9   1/1     Running   0          48s
helloworld-deployment-759fc84489-pclvw   1/1     Running   0          7m4s
helloworld-deployment-759fc84489-rb9nz   1/1     Running   0          48s
helloworld-deployment-759fc84489-w79wt   1/1     Running   0          48s
```

## Let's ScaleIn the Pods from 10 to 3.
```
kubectl scale --replicas=3 deployment helloworld-deployment
```

```
kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-759fc84489-94f2t   1/1     Running   0          9m21s
helloworld-deployment-759fc84489-ltldf   1/1     Running   0          9m21s
helloworld-deployment-759fc84489-pclvw   1/1     Running   0          9m21s
```


## Let's try an detele the pod to check deployment in actions

```
kubectl delete pod helloworld-deployment-759fc84489-94f2t helloworld-deployment-759fc84489-lt ldf
pod "helloworld-deployment-759fc84489-94f2t" deleted
pod "helloworld-deployment-759fc84489-ltldf" deleted
```
```
kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-759fc84489-g4szx   1/1     Running   0          25s
helloworld-deployment-759fc84489-m9v46   1/1     Running   0          25s
helloworld-deployment-759fc84489-pclvw   1/1     Running   0          10m
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
