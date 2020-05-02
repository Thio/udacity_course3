This is my docker/kubernetis project for the udacity cloud developer nano degree.

# How to get it up and running

## Prerequisites
node.js >= 13.0
docker >= 19.0
docker-compose >= 1.23
kubectl >= 1.17
a local kubernetes cluster of some sort (I use docker for windows, be aware that you need to build the docker images on linux (especially the frontend one, otherwise it won't run correctly)) corresponding to Kubernetes 1.17

## Build steps
### Docker
#### Build Docker images
Execute `docker-compose -f udacity-c3-deployment/docker/docker-compose-build.yaml build --parallel` to build all docker files.

#### Upload Docker images
Next you can upload the Docker images to your Docker Hub.
```
docker login -u DOCKER_USERNAME -p DOCKER_PASSWORD
docker push $DOCKER_USERNAME/frontend
docker push $DOCKER_USERNAME/backend-feed
docker push $DOCKER_USERNAME/backend-user
docker push $DOCKER_USERNAME/reverseproxy
```

## Kubernetis
### Configure Kubernetis
Configure the sample configuration files under the folder.
- udacity-c3-deployment/SampleSecretsAndConfig/aws-secret.yaml
- udacity-c3-deployment/SampleSecretsAndConfig/env-secret.yaml
- udacity-c3-deployment/SampleSecretsAndConfig/env-config.yaml

### Apply configuration
```
kubectl apply -f udacity-c3-deployment/SampleSecretsAndConfig/aws-secret.yaml
kubectl apply -f udacity-c3-deployment/SampleSecretsAndConfig/env-secret.yaml
kubectl apply -f udacity-c3-deployment/SampleSecretsAndConfig/env-configmap.yaml
```

### Deploy - Deployment
```
kubectl apply -f udacity-c3-deployment/k8s/backend-feed-deployment.yaml
kubectl apply -f udacity-c3-deployment/k8s/backend-user-deployment.yaml
kubectl apply -f udacity-c3-deployment/k8s/frontend-deployment.yaml
kubectl apply -f udacity-c3-deployment/k8s/reverseproxy-deployment.yaml
```

### Deploy - Service
```
kubectl apply -f udacity-c3-deployment/k8s/backend-feed-service.yaml
kubectl apply -f udacity-c3-deployment/k8s/backend-user-service.yaml
kubectl apply -f udacity-c3-deployment/k8s/frontend-service.yaml
kubectl apply -f udacity-c3-deployment/k8s/reverseproxy-service.yaml
```

### Checks

#### kubectl get pods
```
Thio@DESKTOP-CBQH7J4 D:\Code\udacity_course3
$ kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
backend-feed-latest-7594f7cc65-kntmk   1/1     Running   0          61m
backend-feed-latest-7594f7cc65-wdtdq   1/1     Running   0          61m
backend-user-latest-9765b5f6f-6gcbs    1/1     Running   0          61m
backend-user-latest-9765b5f6f-p7k9j    1/1     Running   0          61m
frontend-latest-594fb95758-d7kgs       1/1     Running   0          61m
frontend-latest-594fb95758-hj2kp       1/1     Running   0          61m
reverseproxy-latest-f46c9849-9sndl     1/1     Running   0          61m
```

#### kubectl get services
```
Thio@DESKTOP-CBQH7J4 D:\Code\udacity_course3
$ kubectl get services
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
backend-feed   ClusterIP   10.100.117.122   <none>        8080/TCP   63m
backend-user   ClusterIP   10.100.240.31    <none>        8080/TCP   63m
frontend       ClusterIP   10.100.109.100   <none>        80/TCP     63m
kubernetes     ClusterIP   10.100.0.1       <none>        443/TCP    133m
reverseproxy   ClusterIP   10.100.33.235    <none>        8080/TCP   63m
```

#### kubectl get deployments
```
Thio@DESKTOP-CBQH7J4 D:\Code\udacity_course3
$ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
backend-feed-latest   2/2     2            2           63m
backend-user-latest   2/2     2            2           63m
frontend-latest       2/2     2            2           63m
reverseproxy-latest   1/1     1            1           63m
```

## Forword ports
```
kubectl port-forward service/frontend 8100:80
kubectl port-forward service/reverseproxy 8080:8080
```

## Done - Enjoy
Navigate to http://localhost:8100