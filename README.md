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
        - name: oauth2_proxy
          image: "yourdockerhub/oauth2_proxy2"
          ports:
            - name: http
              containerPort: 4180
          env:
            - name: OAUTH2_PROXY_CLIENT_ID
              valueFrom:
                configMapKeyRef:
                  name: oauth2_proxy_config
                  key: client_id
            - name: OAUTH_PROXY_CLIENT_SECRET
                configMapKeyRef:
                  name: oauth2_proxy_config
                  key: client_secret
            - name: OAUTH_PROXY_UPSTREAM
                configMapKeyRef:
                  name: oauth2_proxy_config
                  key: proxy_upstream
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

## Marathon

```
{
  "id": "/oauth2_proxy",
  "cmd": null,
  "cpus": 1,
  "mem": 1024,
  "disk": 0,
  "instances": 1,
  "constraints": [
    [
      "hostname",
      "UNIQUE"
    ]
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "raravena80/oauth2_proxy:latest",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 4180,
          "hostPort": 4180,
          "protocol": "tcp",
          "name": "skopos",
          "labels": {}
        }
      ],
      "privileged": false,
      "parameters": [],
      "forcePullImage": false
    }
  },
  "env": {
    "OAUTH2_PROXY_CLIENT_ID": "your-client-id",
    "OAUTH_PROXY_CLIENT_SECRET": "your-client-secret",
    "OAUTH_PROXY_UPSTREAM": "your-upstream"
  }
}
```
