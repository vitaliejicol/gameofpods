#                                                    **Game of Pods - Voting App**


![image](https://user-images.githubusercontent.com/64328755/103560085-cb616100-4e7c-11eb-9ad3-c23934eab3a2.png)

Prerequisites:

1-Make sure have signed up and enrolled for https://kodekloud.com/p/game-of-pods 

Overview

Game of PODs makes learning and practicing your Kubernetes skills fun by providing you with a set of challenges.

Below we will take the Voting App challange and deploy a new application to our cluster.


**A - Configuration of Voting App**

We have request Voting App must run in our `vote` Namespace to create Namespace run command below;

`kubectl create namespace vote`


Create a file `vote-service.yaml` with configuration below

```
apiVersion: v1
kind: Service
metadata:
  namespace: vote
  name: vote-service
spec:
  type: NodePort
  selector:
    app: vote-deployment
  ports:
    - name: http
      protocol: TCP
      port: 5000
      targetPort: 80
      nodePort: 31000
```

Create a file `vote-deployment.yaml` with configuration below

```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: vote
  name: vote-deployment
  labels:
    app: vote-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vote-deployment
  template:
    metadata:
      labels:
        app: vote-deployment
    spec:
      containers:
      - name: vote-deployment
        image: kodekloud/examplevotingapp_vote:before
```

Create a file `redis-service.yaml` with configuration below

```
apiVersion: v1
kind: Service
metadata:
  namespace: vote
  name: redis
spec:
  selector:
    app: redis-deployment
  ports:
    - name: http
      protocol: TCP
      port: 6379
      targetPort: 6379
```

Create a file `redis-deployment.yaml` with configuration below

```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: vote
  name: redis-deployment
  labels:
    app: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-deployment
  template:
    metadata:
      labels:
        app: redis-deployment
    spec:
      containers:
      - name: redis-deployment
        image: redis:alpine
        volumeMounts:
        - mountPath: /data
          name: redis-data
      volumes:
      - name: redis-data
        emptyDir: {}
```

Create a file `worker.yaml` with configuration below

```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: vote
  name: worker
  labels:
    app: worker
spec:
  replicas: 1
  selector:
    matchLabels:
      app: worker
  template:
    metadata:
      labels:
        app: worker
    spec:
      containers:
      - name: worker
        image: kodekloud/examplevotingapp_worker
```

Create a file `db-service.yaml` with configuration below

```
apiVersion: v1
kind: Service
metadata:
  namespace: vote
  name: db
spec:
  selector:
    app: db-deployment
  ports:
    - name: http
      protocol: TCP
      port: 5432
      targetPort: 5432
```

Create a file `db-deployment.yaml` with configuration below

```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: vote
  name: db-deployment
  labels:
    app: db-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: db-deployment
  template:
    metadata:
      labels:
        app: db-deployment
    spec:
      containers:
      - name: postgress
        image: postgres:9.4
        env:
        - name: POSTGRES_PASSWORD
          value: password
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: db-data
      volumes:
      - name: db-data
        emptyDir: {}
```

Create a file `result-service.yaml` with configuration below

```
apiVersion: v1
kind: Service
metadata:
  namespace: vote
  name: result-service
spec:
  type: NodePort
  selector:
    app: result-deployment
  ports:
    - name: http
      protocol: TCP
      port: 5001
      targetPort: 80
      nodePort: 31001
```

Create a file `result-deployment.yaml` with configuration below

```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: vote
  name: result-deployment
  labels:
    app: result-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: result-deployment
  template:
    metadata:
      labels:
        app: result-deployment
    spec:
      containers:
      - name: result
        image: kodekloud/examplevotingapp_result:before
```

**B - Creating of Voting APP**

Run the commands below to create Voting App

`kubectl create -f vote-service.yaml`

`kubectl create -f vote-deployment.yaml`

`kubectl create -f redis-service.yaml`

`kubectl create -f redis-deployment.yaml`

`kubectl create -f worker.yaml`

`kubectl create -f db-service.yaml`

`kubectl create -f db-deployment.yaml`

`kubectl create -f result-service.yaml`

`kubectl create -f result-deployment.yaml`

### Note : Worker Deployment depends on the Database therefor it might take a second to turn it green on quiz portal as it waits for your db-deployment to be ready.
