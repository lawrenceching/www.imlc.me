# Install wiki.js via Kubenetes on macOS

Create wikijs.yml with below content

```
apiVersion:v1
kind:Pod
metadata:
name:wikijs
labels:
env:dev
spec:
containers:
-name:wikijs
image:requarks/wiki:beta
ports:
-containerPort:3000
env:
-name:DB_TYPE
value:"sqlite"
-name:DB_FILEPATH
value:"/tmp/wiki.db"
```

And then run

```
kubectl apply -f ./wikijs.yml
```

By default, you’re allow to access Pod from host network. You will have to expose Pod to host. To do that, you need port forwarding. Run

```
kubectl port-forward wikijs 3000:3000
```
