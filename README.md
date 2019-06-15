### Build the docker image:

>> docker build .

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

>> docker run -p 3000:3000 -it b49c509c2c4e

output:

```
Example app listening at http://:::3000
```

>>> curl localhost:3000

output:
```
Hello World
```

### Push your image to docker registry:

>> docker login

>> docker tag <image id> username/docker-demo

>> docker push username/docker-demo

output:

```
latest: digest: sha256:77b86d1d3750876d4170c612d74b565faaa72f3a2963d85b73bf83276bb2f7c7 size: 2420
```