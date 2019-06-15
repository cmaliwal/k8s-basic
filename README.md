### Build the docker image:

```
docker build .
```

output:

```
npm info ok 
Removing intermediate container 50e6ab14c8d9
 ---> f40b3a6b6b3c
Step 5/6 : EXPOSE 3000
 ---> Running in 0d9a5435d930
Removing intermediate container 0d9a5435d930
 ---> 04bec417ec7d
Step 6/6 : CMD npm start
 ---> Running in d72e3df2b3ab
Removing intermediate container d72e3df2b3ab
 ---> b49c509c2c4e
Successfully built b49c509c2c4e
```

### run docker image:

```
docker run -p 3000:3000 -it b49c509c2c4e
```

output:

```
Example app listening at http://:::3000
```

```
curl localhost:3000
```

output:
```
Hello World
```

### Push your image to docker registry:

```
docker login
docker tag <image id> username/docker-demo
docker push username/docker-demo
```

output:

```
latest: digest: sha256:77b86d1d3750876d4170c612d74b565faaa72f3a2963d85b73bf83276bb2f7c7 size: 2420
```

### Run your first app on kubernet:


##### Create a pod:

```
kubectl create -f pod-helloworld.yml 
```

output:

```
pod/nodehelloworld.example.com created
```

```
kubectl get pod
```

output:

```
NAME                         READY   STATUS              RESTARTS   AGE
nodehelloworld.example.com   0/1     ContainerCreating   0          2m41s
```

#### Describe the pod:

```
kubectl describe pod nodehelloworld.example.com
```

output:

```
Name:               nodehelloworld.example.com
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               minikube/10.0.2.15
Start Time:         Sat, 15 Jun 2019 11:30:37 +0530
Labels:             app=helloworld
Annotations:        <none>
Status:             Running
IP:                 172.17.0.4
Containers:
  k8s-demo:
    Container ID:   docker://0546573b2f787ed21661227305b2f6820df87370a0a09ba2b4a530d13582f753
    Image:          cmaliwal/docker-demo
    Image ID:       docker-pullable://cmaliwal/docker-demo@sha256:77b86d1d3750876d4170c612d74b565faaa72f3a2963d85b73bf83276bb2f7c7
    Port:           3000/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 15 Jun 2019 11:33:31 +0530
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    .....
.....
....
```

#### Port forward:

```
kubectl port-forward nodehelloworld.example.com 8081:3000
```

output:

```
Forwarding from 127.0.0.1:8081 -> 3000
Forwarding from [::1]:8081 -> 3000
```

```
curl localhost:8081
```

output:

```
Hello World!
```

#### Expose:

```
kubectl expose pod nodehelloworld.example.com --type=NodePort --name=helloworld-service
```

output:

```
service/helloworld-service exposed
```

```
minikube service helloworld-service --url
```

outoput:

```
http://192.168.99.101:30740
```

```
kubectl get service
```

output:

```
NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
helloworld-service   NodePort    10.110.176.108   <none>        3000:30740/TCP   4m30s
```

```
curl http://192.168.99.101:30740
```

output:

```
Hello World!
```

### Describe service:

```
kubectl describe service helloworld-service
```

Output:

```
Name:                     helloworld-service
Namespace:                default
Labels:                   app=helloworld
Annotations:              <none>
Selector:                 app=helloworld
Type:                     NodePort
IP:                       10.110.176.108
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  30740/TCP
Endpoints:                172.17.0.4:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

```
kubectl run -i --tty busybox --image=busybox --restart=Never -- sh
```

output: 

```
/ #
telnet 172.17.0.4:3000

Connected to 172.17.0.4:3000

GET /

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 12
ETag: W/"c-Lve95gjOVATpfV8EL5X4nxwjKHE"
Date: Sat, 15 Jun 2019 06:44:26 GMT
Connection: close

Hello World!Connection closed by foreign host
```

#### Execute command on pod:

```
kubectl exec nodehelloworld.example.com -- ls /app
```

Output:
```
Dockerfile
README.md
docker-compose.yml
index-db.js
index.js
node_modules
package-lock.json
package.json
test
```