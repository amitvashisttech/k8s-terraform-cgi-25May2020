# Service
An abstract way to expose an application running on a set of Pods as a network service.

With Kubernetes you don’t need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their own IP addresses and a single DNS name for a set of Pods, and can load-balance across them.

# Motivation
Kubernetes Pods are mortal. They are born and when they die, they are not resurrected. If you use a Deployment to run your app, it can create and destroy Pods dynamically.

Each Pod gets its own IP address, however in a Deployment, the set of Pods running in one moment in time could be different from the set of Pods running that application a moment later.

This leads to a problem: if some set of Pods (call them “backends”) provides functionality to other Pods (call them “frontends”) inside your cluster, how do the frontends find out and keep track of which IP address to connect to, so that the frontend can use the backend part of the workload?

# Service resources
In Kubernetes, a Service is an abstraction which defines a logical set of Pods and a policy by which to access them (sometimes this pattern is called a micro-service). The set of Pods targeted by a Service is usually determined by a selector.

# Defining a Service

A Service in Kubernetes is a REST object, similar to a Pod. Like all of the REST objects, you can POST a Service definition to the API server to create a new instance. The name of a Service object must be a valid DNS label name.

For example, suppose you have a set of Pods that each listen on TCP port 9376 and carry a label "app=MyApp".

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

This specification creates a new Service object named “my-service”, which targets TCP port 9376 on any Pod with the app=MyApp label.

Kubernetes assigns this Service an IP address (sometimes called the “cluster IP”), which is used by the Service proxies.

The controller for the Service selector continuously scans for Pods that match its selector, and then POSTs any updates to an Endpoint object also named “my-service”.

Note: A Service can map any incoming port to a targetPort. By default and for convenience, the targetPort is set to the same value as the port field.

Port definitions in Pods have names, and you can reference these names in the targetPort attribute of a Service. This works even if there is a mixture of Pods in the Service using a single configured name, with the same network protocol available via different port numbers. This offers a lot of flexibility for deploying and evolving your Services. For example, you can change the port numbers that Pods expose in the next version of your backend software, without breaking clients.

## Create Service
### First will deploy a helloworld "Tiny Web" Pod / Deployment. 

### Create Service 

### Second will deploy Python-Web App Pod. 

### Third will deploy Tomcat-App Pod. 

### Describe the Service & Pod IP Address. 
If you notice all the Pod has been configured as endpoint for helloworld service become we have used the same selector for all my POD Deployment & Target port also with the abstracted name nodejs-port. 


# How to delete the Deployment & associated Service

### Deployment Deletion
```
```

### Service Deletion
```
```
