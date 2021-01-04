#                                                    **Game of Pods - Iron Gallery**


![image](https://user-images.githubusercontent.com/64328755/103563508-80e2e300-4e82-11eb-8da5-89a6a14b9bde.png)

### Overview

Game of PODs makes learning and practicing your Kubernetes skills fun by providing you with a set of challenges.

Below we will take the Iron Gallery challange;
   - Create a Lychee Gallery and MariaDB Deployment
   - Create Services for iron-gallery and iron-db
   - Create Ingress Resource
   - Create Volumes for the Deployment
   - Apply NetworkPolicy on Pods
  
   
   
   

### Prerequisites:

1-Make sure have signed up and enrolled for https://kodekloud.com/p/game-of-pods 





> **A - Configuration of Iron Gallery**

**Deploying the application and exposing thru services**

Create a file `ingress.yaml` with configuration below

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: iron-gallery-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: iron-gallery-braavos.com
      http:
        paths:
        - path: /
          backend:
            serviceName: ingress-spacehttp
            servicePort: 80
        - path: /
          backend:
            serviceName: iron-gallery-service
            servicePort: 30099
```

Create a file `iron-service.yaml` with configuration below

```
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service
spec:
  type: NodePort
  selector:
    run: iron-gallery
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30099
```

Create a file `iron-deployment.yaml` with configuration below

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery
  labels:
    run: iron-gallery
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
      - name: iron-gallery
        image: kodekloud/irongallery:2.0
        ports:
        - containerPort: 3306
          name: mysql
          protocol: TCP
        volumeMounts:
          - mountPath: /usr/share/nginx/html/uploads
            name: images
          - mountPath: /usr/share/nginx/html/data
            name: config
        resources:
          limits:
            memory: "100Mi"
            cpu: "50m"
      volumes:
      - name: config
        emptyDir: {}
      - name: images
        emptyDir: {}
```

Create a file `iron-db-service.yaml` with configuration below

```
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service
spec:
  selector:
    db: mariadb
  ports:
    - protocol: TCP
      port: 3306
```

Create a file `iron-db-deployment.yaml` with configuration below

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db
  labels:
    db: mariadb
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
      - image: kodekloud/irondb:2.0
        name: iron-db
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "Braavo"
        - name: MYSQL_DATABASE
          value: "lychee"
        - name: MYSQL_PASSWORD
          value: "lychee"
        - name: MYSQL_USER
          value: "lychee"
        ports:
        - containerPort: 3306
          name: mysql
          protocol: TCP
        volumeMounts:
          - mountPath: /var/lib/mysql
            name: db
      volumes:
      - name: db
        emptyDir: {}
```

Create a file `iron-networkpolicy.yaml` with configuration below

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: iron-gallery-firewall
spec:
  podSelector:
    matchLabels:
      db: mariadb
  ingress:
  - ports:
    - port: 3306
    from:
    - podSelector:
        matchLabels:
          run: iron-gallery
```


**B - Creating of Iron Gallery Resources**

Run the commands below to create Iron Gallery Resources

`kubectl create -f ingress.yaml`

`kubectl create -f iron-service.yaml`

`kubectl create -f iron-deployment.yaml`

`kubectl create -f iron-db-service.yaml`

`kubectl create -f iron-db-deployment.yaml`

`kubectl create -f iron-networkpolicy.yaml`







if you successfully completed the challange you will get the message below ! 

## *yamlio muño ēngos ñuhys issa. Avy jorrāelan, yamlio!*

