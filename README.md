# oauth2_proxy_docker

This is a Dockerfile for [oauth2_proxy](https://github.com/bitly/oauth2_proxy)


## Build

docker build . -t yourdockerhub/oauth2_proxy:latest

## Run

```
docker run -d -p 4180:4180 \
    -e OAUTH2_PROXY_CLIENT_ID=... \
    -e OAUTH2_PROXY_CLIENT_SECRET=... \
    -e OAUTH2_PROXY_UPSTREAM=... \
    yourdockerhub/oauth2_proxy
```

## K8s


deployment.yml
```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: oauth2_proxy
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: oauth2_proxy
        tier: backend
        track: stable
    spec:
      containers:
        - name: hello
          image: "yourdockerhub/oauth2_proxy2"
          ports:
            - name: http
              containerPort: 4180
```
Create the deployment
```
kubectl create -f deployment.yml
```

service.yml
```
kind: Service
apiVersion: v1
metadata:
  name: oauth2_proxy
spec:
  selector:
    app: oauth2_proxy
    tier: backend
  ports:
  - protocol: TCP
    port: 4180
    targetPort: http
```

Create the service
```
kubectl create -f service.yml
```
