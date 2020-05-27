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

```
```
