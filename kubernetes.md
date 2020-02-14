# MINIKUBE

## Habilitar Hyper-V en windows 10
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
también:
```
DISM /Online /Enable-Feature /All /FeatureName:Microsoft-Hyper-V
```
## Asignar el environment de docker a minikube
```
> minikube docker-env
> minikube docker-env | Invoke-Expression
```
## Arrancar minikube

Debe estar creado con el Administrador de Hyper-V el conmutador virtual VM-External-Switch como external a la red del equipo (eth0 o wlan)
```
minikube start --vm-driver "hyperv" --hyperv-virtual-switch "VM-External-Switch"
```


# KUBERNETES

# PODS

### Crear un pod
```
kubectl run --generator=run-pod/v1 podtest --image=nginx:alpine
```

### Listar pods
```
kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
podtest   1/1     Running   0          23s
```
READY 1 contenedor de 1 contenedor esperado, esta la estrategia más utilizada un contenedor por pod

### Ver logs del pod
```
kubectl describe pod podtest
Name:         podtest
Namespace:    default
Priority:     0
Node:         minikube/172.17.107.109
Start Time:   Tue, 11 Feb 2020 16:08:07 +0100
Labels:       run=podtest
Annotations:  <none>
Status:       Running
IP:           172.18.0.4
IPs:
  IP:  172.18.0.4
Containers:
  podtest: ...
```

### Eliminar pod
```
kubectl delete pod nombre
```

### Información del pod con salida yaml
```
kubectl get pod podtest -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2020-02-11T15:08:06Z"
  labels:
    run: podtest
  name: podtest
  namespace: default
  resourceVersion: "6180"
  selfLink: /api/v1/namespaces/default/pods/podtest
  uid: 2d730bf6-f22c-4be9-8a66-3c75207652c4
```

### Ver los logs
```
172.18.0.1 - - [11/Feb/2020:15:43:10 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.66.0" "-"
172.18.0.1 - - [11/Feb/2020:15:44:15 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.66.0" "-"
172.18.0.1 - - [11/Feb/2020:15:44:41 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.66.0" "-"
172.18.0.1 - - [11/Feb/2020:15:47:40 +0000] "GET / HTTP/1.1" 200 3 "-" "curl/7.66.0" "-"
```

### Ver los contenedores
```
minikube ssh

docker ps -f name=podtest

PS C:\Windows\system32> kubectl exec -ti podtest -- sh
/ #
```

### Versiones del api para manifiestos yaml
```
kubectl api-versions
```

### Recursos para manifiestos yaml, en este caso relacionados con pods
```
kubectl api-resources | grep Pod
pods                              po                                          true         Pod
podtemplates                                                                  true         PodTemplate
horizontalpodautoscalers          hpa          autoscaling                    true         HorizontalPodAutoscaler
poddisruptionbudgets              pdb          policy                         true         PodDisruptionBudget
podsecuritypolicies               psp          policy                         false        PodSecurityPolicy
```

### Aplicar manifiesto yaml para crear objeto
```
kubectl apply -f pod.yaml
pod/podtest2 created
```

### Eliminar en base a manifiesto
```
kubectl delete -f pod.yaml
```

### Lista de pods, informa IP y STATUS
```
kubectl get pods -o=custom-columns=NAME:.metadata.name,IP:.status.podIP
```

### Logs de un contenedor
```
kubectl logs doscont -c cont1
```

### Comprobar contenedor
```
kubectl exec -ti doscont -- sh
```

## Labels

### Labels metadata en manifiestos
```
kubectl get pods -l app=backend
kubectl get pods -l app=frontend
kubectl get pods -l env=dev
```

# REPLICASETS
```
kubectl get rs rs-test -o yaml
```

kubectl run --generator=run-pod/v1 podtest1 --image=nginx:alpine
kubectl run --generator=run-pod/v1 podtest2 --image=nginx:alpine

Incluir label al pod que esta corriendo (kubectl label pods)
kubectl label pods podtest1 app=pop-label

# DEPLOYMENTS

```
kubectl get deployments --show-labels
```

```
kubectl rollout status deployment deployment-test
deployment "deployment-test" successfully rolled out
```

```
kubectl get rs --show-labels
NAME                         DESIRED   CURRENT   READY   AGE     LABELS
deployment-test-86677fd487   3         3         3       3m51s   app=front,pod-template-hash=86677fd487
```
```
kubectl get po --show-labels
NAME                               READY   STATUS    RESTARTS   AGE     LABELS
deployment-test-86677fd487-bhz5c   1/1     Running   0          5m16s   app=front,pod-template-hash=86677fd487
deployment-test-86677fd487-h6s7g   1/1     Running   0          5m16s   app=front,pod-template-hash=86677fd487
deployment-test-86677fd487-qp88x   1/1     Running   0          5m16s   app=front,pod-template-hash=86677fd487
```

```
kubectl get deployments deployment-test -o yaml
```

## Ver eventos del deployment

```
kubectl describe deployment deployment-test
Name:                   deployment-test
Namespace:              default
CreationTimestamp:      Wed, 12 Feb 2020 11:48:15 +0100
Labels:                 app=front
Annotations:            deployment.kubernetes.io/revision: 3
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"front"},"name":"deployment-test","namespace":"de...
Selector:               app=front
Replicas:               3 desired | 2 updated | 4 total | 3 available | 1 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=front
  Containers:
   nginx:
    Image:        nginx:alpine
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  deployment-test-84467d95b6 (2/2 replicas created)
NewReplicaSet:   deployment-test-86677fd487 (2/2 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  20m    deployment-controller  Scaled up replica set deployment-test-86677fd487 to 3
  Normal  ScalingReplicaSet  3m35s  deployment-controller  Scaled up replica set deployment-test-84467d95b6 to 1
  Normal  ScalingReplicaSet  2m58s  deployment-controller  Scaled down replica set deployment-test-86677fd487 to 2
  Normal  ScalingReplicaSet  2m58s  deployment-controller  Scaled up replica set deployment-test-84467d95b6 to 2
  Normal  ScalingReplicaSet  2m48s  deployment-controller  Scaled down replica set deployment-test-86677fd487 to 1
  Normal  ScalingReplicaSet  2m48s  deployment-controller  Scaled up replica set deployment-test-84467d95b6 to 3
  Normal  ScalingReplicaSet  2m39s  deployment-controller  Scaled down replica set deployment-test-86677fd487 to 0
  Normal  ScalingReplicaSet  15s    deployment-controller  Scaled up replica set deployment-test-86677fd487 to 1
  Normal  ScalingReplicaSet  7s     deployment-controller  Scaled down replica set deployment-test-84467d95b6 to 2
  Normal  ScalingReplicaSet  7s     deployment-controller  Scaled up replica set deployment-test-86677fd487 to 2
```

Los replicasets se quedan en el historico despues de la actualización, por defecto máximo 10 revisionHistoryLimit

```
> kubectl get rs -l app=front
NAME                         DESIRED   CURRENT   READY   AGE
deployment-test-84467d95b6   0         0         0       5m28s
deployment-test-86677fd487   3         3         3       22m

> kubectl rollout history deployment deployment-test
deployment.apps/deployment-test
REVISION  CHANGE-CAUSE
2         <none>
3         <none>

> kubectl rollout history deployment deployment-test
deployment.apps/deployment-test
REVISION  CHANGE-CAUSE
2         <none>
3         <none>

> kubectl apply -f .\dev.yaml
deployment.apps/deployment-test configured

> kubectl rollout history deployment deployment-test
deployment.apps/deployment-test
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
4         <none>

> kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
deployment-test-7995b9c4f7   2         2         1       13s
deployment-test-84467d95b6   0         0         0       8m11s
deployment-test-86677fd487   2         2         2       24m
> kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
deployment-test-7995b9c4f7   3         3         2       19s
deployment-test-84467d95b6   0         0         0       8m17s
deployment-test-86677fd487   1         1         1       25m
> kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
deployment-test-7995b9c4f7   3         3         2       24s
deployment-test-84467d95b6   0         0         0       8m22s
deployment-test-86677fd487   1         1         1       25m
> kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
deployment-test-7995b9c4f7   3         3         3       27s
deployment-test-84467d95b6   0         0         0       8m25s
deployment-test-86677fd487   0         0         0       25m
```

## Utilizar CHANGE-CAUSE

```
> kubectl apply -f .\dev.yaml --record
deployment.apps/deployment-test configured

> kubectl rollout history deployment deployment-test
deployment.apps/deployment-test
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl.exe apply --filename=.\dev.yaml --record=true
```

Si incluimos metadata:   
annotations:
    kubernetes.io/change-cause: "Cambio de puerto 90"

```
> kubectl rollout history deployment deployment-test
deployment.apps/deployment-test
REVISION  CHANGE-CAUSE
2         kubectl.exe apply --filename=.\dev.yaml --record=true
3         Cambio de puerto 90
```

Para ver los cambios

```
> kubectl rollout history deployment deployment-test --revision=3
deployment.apps/deployment-test with revision #3
Pod Template:
  Labels:       app=front
        pod-template-hash=7995b9c4f7
  Annotations:  kubernetes.io/change-cause: Cambio de puerto 90
  Containers:
   nginx:
    Image:      nginx:alpine
    Port:       90/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
  
```

## Utilizar ROLLBACK para marcha atrás de despliegue

```
> kubectl rollout history deployment deployment-test
deployment.apps/deployment-test
REVISION  CHANGE-CAUSE
2         kubectl.exe apply --filename=.\dev.yaml --record=true
3         Cambio de puerto 90
4         Cambio de puerto 90
5         Despliegue mal configurado por imagen

> kubectl get pods
NAME                               READY   STATUS              RESTARTS   AGE
deployment-test-7995b9c4f7-4lzsf   1/1     Running             0          6m54s
deployment-test-7995b9c4f7-j2grb   1/1     Running             0          7m3s
deployment-test-7995b9c4f7-jdjl5   1/1     Running             0          7m10s
deployment-test-ccf97ff6c-nbmqr    0/1     ContainerCreating   0          8s

> kubectl rollout undo deployment deployment-test --to-revision=3
deployment.apps/deployment-test rolled back

> kubectl rollout status deployment deployment-test
deployment "deployment-test" successfully rolled out

> kubectl rollout history deployment deployment-test
deployment.apps/deployment-test
REVISION  CHANGE-CAUSE
2         kubectl.exe apply --filename=.\dev.yaml --record=true
4         Cambio de puerto 90
5         Despliegue mal configurado por imagen
6         Cambio de puerto 90
```


# SERVICES

Los tipos de servicio son: ClusterIP, NodePort y LoadBalancer

```
> kubectl get svc
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.96.0.1     <none>        443/TCP    21h
my-service   ClusterIP   10.98.183.0   <none>        8080/TCP   5m49s
```

```
> kubectl describe svc my-service
Name:              my-service
Namespace:         default
Labels:            app=front
Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"labels":{"app":"front"},"name":"my-service","namespace":"default"},"spec...
Selector:          app=front
Type:              ClusterIP
IP:                10.98.183.0
Port:              <unset>  8080/TCP
TargetPort:        80/TCP
Endpoints:         172.18.0.4:80,172.18.0.7:80,172.18.0.8:80
Session Affinity:  None
Events:            <none>
```

## Ver endpoints
```
> kubectl get endpoints
NAME         ENDPOINTS                                   AGE
kubernetes   172.17.107.109:8443                         21h
my-service   172.18.0.4:80,172.18.0.7:80,172.18.0.8:80   11m
```

```
> kubectl get pods -l app=front -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
deployment-test-86677fd487-8ms9j   1/1     Running   0          11m   172.18.0.8   minikube   <none>           <none> 
deployment-test-86677fd487-9zbxt   1/1     Running   0          11m   172.18.0.4   minikube   <none>           <none> 
deployment-test-86677fd487-bwxgx   1/1     Running   0          11m   172.18.0.7   minikube   <none>           <none>
```

### Buscar propiedades del deployment
```
> kubectl get deployments backend-k8s-hands-on -o yaml | grep -i PullPolicy
```

### Imagen temporal cuando se sale del ssh se elimina, validación de otros pods
```
> kubectl run --rm -ti --generator=run-pod/v1 podtest3 --image=nginx:alpine -- sh
```
