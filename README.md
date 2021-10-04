
# CKA or CKAD certification exams practices sample

Are you preparation for  CKA or CKAD certification exams, here is some of the practices sample that you can practices. Please note that these question not similar to actual exams. This will help you to familiarize yourself as you will be required to demonstrate knowledge of each topic in order to pass the exam.

#### Environment setup follow the below setup

``` bash

echo -e "MY_ID=faizal\nMY_PWD=1234" > my-configmap.txt

alias k='kubectl'
alias kgp='kubectl get pods'e
alias kgs='kubectl get service'
alias kd='kubectl delete'
alias kcf='kubectl create -f'
alias kaf='kubectl apply -f'
alias kgpa='kubectl get pods --all-namespaces'
export yaml = '--dry-run=client -o yaml'

vi ~/.vimrc
sset number
set tabstop=2
set expandtab
set shiftwidth=2
set cursorline

kubectl apply -f https://raw.githubusercontent.com/mofaizal/cka-and-ckad-certification-exams-practices-samp/main/setup/setup.yaml
``` 
## Practices Question #1 

### Context 

Create application deployment called car-mart using kubectl 

### Taks 
-	All objects should be in the ``` cars ``` namespace. This namespace already exists in the cluster.
-	The deployment should be named ``` car-mart-webapp. ``` 
-	The deployment should have ``` 4 replicas. ``` 
-	The deployment's pods should have one container using the ``` nginx ```  image with the tag ``` 1.19 ``` 

<details><summary> show me the solution </summary>

```bash
kubectl create deployment --namespace cars car-mart-webapp --image=nginx:1.19 --replicas=3
```
</details>

## Practices Question #2

### Context 

You are running an application called ``` car-mart ``` app in Kubernetes. You have deployed the app as ``` car-mart-webapp ``` in ``` cars ``` namespace. The deployment of ``` car-mart ``` app done via kubectl. You don’t have backup so you boss ask you to persist the configuration. You need to export the ``` car-mart ```  app deployment and associated configuration in ``` car-mart-webapp.yaml ``` file. 

### Taks
-	Export car-mart configuration to yaml file

<details><summary> show me the solution </summary>

```bash
kubectl get deployment -n cars car-mart-webapp -o yaml > car-mart-webapp.yaml
```
</details>


## Practices Question #3

### Context 

You have running application name called ``` super-store ``` in the ``` apps ``` namesspace. You need to explose the deployment to exteranl communication. 


### Taks
-	Expose a service for ``` super-store ``` deployment and allow external traffic communicate to web server. 
-	Expose the port to ``` 8080 ``` and name the servic to ``` super-store-service ```

<details><summary> show me the solution </summary>

```bash
kubectl -n apps expose deployment super-store --port=8080 --target-port=80 --name=super-store-service
```
To validate the connectivity is working. you need to connect to the service. if you are going across namespaces, you should use <service-name>.<service-namespace>:<service-port> as the service address. within namespace you dont required to specifiy the service namespace. 

```bash
kubectl run busybox-temp --image=busybox -it --rm --restart=Never -- wget -qO- -T 3 http://super-store-service.apps:8080

OR 

kubectl run busybox-temp -n apps --image=busybox -it --rm --restart=Never -- wget -qO- -T 3 http://super-store-service:8080

```

</details>

## Practices Question #4

### Context 

Create application deployment called cloud-store using kubectl 

### Taks 
-	All objects should be in the ``` cloud ``` namespace. This namespace already exists in the cluster.
-	The pod should be named ``` cloud-store ```  
-	The deployment's pods should have one container using the ``` nginx```  image with the tag ``` 1.21 ``` 

<details><summary> show me the solution</summary>

```bash
kubectl run -n cloud cloud-store --image=nginx:1.21
```
</details>


## Practices Question #5

### Context 

You need to decouple configuration artifacts from image content to keep containerized applications portable. 
Create ConfigMaps and configure Pods using data stored in ConfigMaps.

### Taks 
-	Create a ConfigMap called ``` my-config ``` from existing file name ``` my-configmap.txt ```
-	Create a pod named ``` my-pod ```  uses the environment variables from the ConfigMap.
-	Use container image  ``` nginx```  with the tag ``` 1.21 ``` 
-   Shell into the Pod ``` my-pod ``` and validate the env by printing out the created environment variables.
-   Inside the pod store the output txt file called ``` configmap-value.txt ``` 

<details><summary> show me the solution</summary>

Create ConfigMap using existing file 
```bash
kubectl create configmap my-config --from-env-file=my-configmap.txt
```
Creat a pod using dry-run and output as a yaml to edit 
```bash
kubectl run my-pod --image=nginx:1.21 --restart=Never -o yaml --dry-run > my-config-pod.yaml
```
use vi editor to edit the file 
```bash
vi my-config-pod.yaml
```
orginal file will look like below 

```yaml
  1 apiVersion: v1           
  2 kind: Pod
  3 metadata:
  4   creationTimestamp: null
  5   labels:
  6     run: my-pod
  7   name: my-pod
  8 spec:
  9   containers:
 10   - image: nginx:1.21
 11     name: my-pod
 12     resources: {}
 13   dnsPolicy: ClusterFirst
 14   restartPolicy: Never
 15 status: {}
```
Edit the yaml file and add lines 12,13 and 14. The final YAML file should look similar to the following code snippet.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: my-pod
  name: my-pod
spec:
  containers:
  - image: nginx:1.21
    name: my-pod
    envFrom:
    - configMapRef:
        name: my-config
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
Create the Pod using the YAML file.

```bash
kubectl create -f my-config-pod.yaml
```

```bash
kubectl exec my-pod -it -- /bin/sh
env
```
```bash
env > configmap-value.txt
cat configmap-value.txt
exit 
```
</details>


## Practices Question #6

### Context 

This deployment with YAML file, Create Pod with two container. 

### Taks 
-   The container one name is ``` web ``` uses the image ``` ngixn ``` 
-   The container two name is ``` app ``` uses the image ``` busybox ``` 
-   The POD should run on ``` web-app ``` namespace
-   Find out the pod status and details 
-   Print the error message into txt file ``` web-app-event.txt ```


<details><summary> show me the solution</summary>

First check is web-app namespace is there or not. if not Create web-app Namespace 

```bash
kubectl create namespace web-app
```

```bash
kubectl --namespace  web-app run web-app --image=busybox --restart=Never -o yaml --dry-run > app.yaml

```

``` yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: web-app
  name: web-app
  namespace: web-app
spec:
  containers:
  - image: ngixn
    name: web
  - image: busybox
    name: app
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```bash
kubectl create -f app.yaml

kubectl get pod -n web-app

kubectl delete pod web-app1 -n web-app

kubectl -n web-app get events  > web-app-event.txt

```

</details>


## Practices Question #7

### Context 

You have running pod, called ``` sg-count ``` retrive the logs and store into file. 

### Taks 
-	You have ``` sg-count ``` pod runing on one of the namespace, find out which namespace.
-	Retrieve the application logs from running pod and store in the file. ``` sg-count-log.txt ```   

<details><summary> show me the solution</summary>

```bash
kubectl get pod -n ALL
kubectl logs sg-count -n cloud > sg-count-log.txt
```
</details>

## Practices Question #8

### Context 

You are task to do PersistentVolume, PersistentVolumeClaim and mount the volume to pod deployment. 

### Taks 

-   You need to create a PersistentVolume named ```sg-db-pv. ``` It should have a capacity of `` 100Mi ``, accessMode ``` ReadWriteOnce ```,   hostPath ``` /volumes/data ``` and  storageClassName ``` auto ```.
-   You need to create a new PersistentVolumeClaim in Namespace ``` sg-db ``` named ``` sg-db-pvc ```. It should request ``` 100Mi ``` storage, accessMode ``` ReadWriteOnce ``` and define a storageClassName ``` auto ```. Make sure that PVC should bound to the PV correctly.
-   Create a new Deployment ``` sg-db-pod ``` in Namespace ``` sg-db ``` which mounts that volume at ``` /tmp/sg-data ```. The Pods of that Deployment should be of image ``` httpd:2.4.49-alpine ```.

<details><summary> show me the solution</summary>

For more details visit : https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolume 

Create PersistentVolume using Yaml 

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: sg-db-pv
  namespace: sg-db
  labels:
    type: local
spec:
  storageClassName: auto
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/volumes/data"
```
```bash
kubectl create -f pv.yaml
kubectl get pv
```

``` yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: sg-db-pvc
  namespace: sg-db
spec:
  storageClassName: auto
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
```
```bash
kubectl create -f pvc.yaml
kubectl get pvc -n sg-db
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sg-db-pod
  namespace: sg-db
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: sg-db-pvc
  containers:
    - name: sg-db-pod
      image: httpd:2.4.49-alpine
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/tmp/sg-data"
          name: task-pv-storage
```
</details>

## Practices Question #9

### Context 

You need to perform Deployment and Rolling updates. Use yaml for deployment

### Taks

-	Create deployment in  ``` web-update ``` namespace. This namespace already exists in the cluster.
-	The deployment should be named ``` web-update. ``` 
-	The deployment should have ``` 1 replicas. ``` 
-	The deployment's pods should have one container using the ``` nginx ```  image with the tag ``` 1.16 ``` 
-   Scale application to ``` 3 replicas ```
-   Web team come up new version and required you to perform upgrade the image name is  ``` nginx ```  image with the tag ``` 1.19 ``` 
-   Check rollout history and export to file ``` web-history-log.txt ```
-   Check rollout history and export
-   Web team found that the new image having issue, the required you to rollback the deployment. Perform rollback.

<details><summary> show me the solution</summary>

Create deployment using yaml script 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-update
  namespace: web-update
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16
```
edit the deployent and update the replicas 

```bash
kubectl edit deployment web-update -n web-update 
```
to view rollout history 

```bash
kubectl rollout history deployment -n web-update
```

```bash
kubectl rollout history deployment -n web-update > web-history-log.txt
```

```bash
kubectl -n web-update set image deployment/web-update nginx=nginx:1.19
```

```bash
kubectl describe deployment -n web-update web-update
```

```bash
kubectl rollout history deployment -n web-update
```

```bash
kubectl rollout undo deployment/web-update -n web-update
```
</details>


For CKA learn about Upgrading Kubernetes with Kubeadm, Implement Backup and Restore Methodologies

### To clean up your practices environment run below commands

```bash

kubectl delete deployment --namespace apps super-store

kubectl delete deployment --namespace cars car-mart-webapp

rm car-mart-webapp.yaml
rm my-configmap.txt
rm my-config-pod.yaml
rm web-app-event.txt 
rm web-history-log.txt
rm car-mart-webapp.yaml 

kubectl delete service super-store-service --namespace apps

kubectl delete deployment --namespace cloud cloud-store

kubectl delete configmap my-config

kubectl delete pod my-pod 

kubectl delete pod web-app --namespace web-app

kubectl delete pod sg-count --namespace cloud

kubectl delete pod sg-db-pod --namespace sg-db

kubectl delete pvc sg-db-pvc -namespace sg-db

kubectl delete deployment --namespace web-update web-update

kubectl delete pv sg-db

kubectl delete namespace cars

kubectl delete namespace cloud

kubectl delete namespace apps 

kubectl delete namespace sg-db 

kubectl delete namespace web-update

```