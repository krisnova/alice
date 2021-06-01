# alice

My main kubernetes toolchain and configuration manager.

### Install

```bash

git clone git@github.com:kris-nova/alice.git /opt/alice
ln -s /opt/alice/alice /usr/local/bin/alice

cd ~/myproject
alice init
# edit Alicefile
alice build
# edit alice/deploy.yaml
alice deploy
alice uninstall
```

### Alice Conventions

##### Alicefile

Every repository you want to manage needs an `Alicefile`

 - TODO: We can infer a lot of this detail

```bash
# Alicefile
#!/bin/bash
REPO="krisnova"
NAME="myproject"
TAG="latest"
SHA="$(git rev-parse HEAD)"
```

##### Alicefile.cmd

These are ran for specific commands.

`Alicefile.push` will be executed during `alice push`.

Available commands

 - build (build and tag docker container)
 - deploy (build and install)
 - init (stub out a new alice project)
 - install (install into kubernetes apply -f)
 - push (push to registry)
 - uninstall (uninstall into kubernetes delete -f)

```bash
# Alicefile.build
make
make clean
```

##### /alice

Place `*.yaml` files here.
These are what will be installed/uninstalled in Kubernetes.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{"deployment.kubernetes.io/revision":"1"},"labels":{"app":"nivenly"},"name":"nivenly","namespace":"default","resourceVersion":"604003"},"spec":{"progressDeadlineSeconds":600,"replicas":1,"revisionHistoryLimit":10,"selector":{"matchLabels":{"app":"nivenly"}},"strategy":{"rollingUpdate":{"maxSurge":"25%","maxUnavailable":"25%"},"type":"RollingUpdate"},"template":{"metadata":{"creationTimestamp":null,"labels":{"app":"nivenly"}},"spec":{"containers":[{"image":"krisnova/nivenly.com:latest","imagePullPolicy":"Always","name":"nivenly","ports":[{"containerPort":80,"protocol":"TCP"}],"resources":{},"terminationMessagePath":"/dev/termination-log","terminationMessagePolicy":"File"}],"dnsPolicy":"ClusterFirst","restartPolicy":"Always","schedulerName":"default-scheduler","securityContext":{},"terminationGracePeriodSeconds":30}}}}
  generation: 1
  labels:
    app: nivenly
  name: nivenly
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nivenly
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: nivenly
    spec:
      containers:
        - image: krisnova/nivenly.com:latest
          imagePullPolicy: Always
          name: nivenly
          ports:
            - containerPort: 80
              protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nivenly
  name: nivenly
  namespace: default
spec:
  clusterIP: 10.105.200.197
  clusterIPs:
    - 10.105.200.197
  externalTrafficPolicy: Cluster
  ports:
    - nodePort: 30000
      port: 1313
      protocol: TCP
      targetPort: 80
  selector:
    app: nivenly
  type: NodePort
```
