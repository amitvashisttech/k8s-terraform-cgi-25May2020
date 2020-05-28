# Helm

## Install helm
```
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.14.1-linux-amd64.tar.gz
tar -xzvf helm-v2.14.1-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
```

## Initialize helm

```
kubectl create -f helm-rbac.yaml
helm init --service-account tiller
```


## Install Redis with helm
```
helm search redis 
helm install redis/stable
```

## Install Redis with cutome release name
```
helm install -n myredis redis/stable
```


## List all helm realses
```
helm list 
```

## Status of Release Components/objects
```
helm status release-name
```

## Delete helm release
```
helm delete release-name
```
