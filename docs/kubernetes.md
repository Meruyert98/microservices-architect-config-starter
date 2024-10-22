## Micro services on kubernetes | deploying micro services on kubernetes

Write manifest file for reactjs ui
```kube
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  "react-ui"
  namespace: ms
  labels:
    app:  "react-ui"
spec:
  selector:
    matchLabels:
      app: "react-ui"
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app:  "react-ui"
    spec:
      containers:
      - name:  "react-ui"
        image:  meruyerts/ui
        ports:
        - containerPort:  8080
          name:  "react-ui"

---

apiVersion: v1
kind: Service
metadata:
  name: "react-ui"
  namespace: ms
spec:
  selector:
    app: "react-ui"
  type: NodePort
  ports:
  - name: "react-ui"
    port: 8080
    targetPort: 8080
````

Check for nodes
```
kubectl get nodes
```

Create namespace
```
kubectl create ns ms
```

Check what is the current namespace
```
kubectl config view
```

Point our config to our ms namespace
```
kubectl config set-context --current --namespace=ms
```

You can check that current namespace changed to ms
```
kubectl config view
```

Check
```
kubectl get all
```

Apply k8s manifest file
```
kubectl apply -f ./ui-web-app-reactjs/kube.yaml
```

Verify that pods are running and services have been created
```
kubectl get all
```

Get ip of the node
```
kubectl get po -o wide
```

You can see external IP
```
kubectl get nodes -o wide
```

Zuul
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  "zuul-api-gateway"
  namespace: ms
  labels:
    app:  "zuul-api-gateway"
spec:
  selector:
    matchLabels:
      app: "zuul-api-gateway"
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app:  "zuul-api-gateway"
    spec:
      containers:
      - name:  "zuul-api-gateway"
        image:  meruyerts/zuul
        ports:
        - containerPort:  9999
          name:  "zuul-api"
      affinity:
          podAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - react-ui
              topologyKey: "kubernetes.io/hostname"

        
---

apiVersion: v1
kind: Service
metadata:
  name: "zuul-service"
  namespace: ms
spec:
  selector:
    app: "zuul-api-gateway"
  type: NodePort
  ports:
  - name: "zuul-service"
    port: 9999
    targetPort: 9999
    nodePort: 32470
```

```
kubectl apply -f ./zuul-api-gateway/kube.yaml
```

Check if they are running in the same node
```
kubectl get all -o wide
```

Shoe

Kubernetes manifest yaml file
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  "shoe"
  namespace: ms
  labels:
    app:  "shoe"
spec:
  selector:
    matchLabels:
      app: "shoe"
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app:  "shoe"
    spec:
      containers:
      - name:  "shoe"
        image:  meruyerts/shoe
        ports:
        - containerPort:  1002
          name:  "shoe"
      
--- 
apiVersion: v1
kind: Service
metadata:
  name: shoe
  namespace: ms
spec:
  selector:
    app: "shoe"
  type: ClusterIP
  ports:
  - name: "shoe"
    port: 1002
    targetPort: 1002
```

```
kubectl apply -f ./shoes-microservice-spring-boot/kube.yaml
```

Check if they are running in the same node
```
kubectl get all -o wide
```

Offer

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  "offer"
  namespace: ms
  labels:
    app:  "offer"
spec:
  selector:
    matchLabels:
      app: "offer"
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app:  "offer"
    spec:
      containers:
      - name:  "offer"
        image:  meruyerts/offer
        ports:
        - containerPort:  1001
          name:  "offer"

---

apiVersion: v1
kind: Service
metadata:
  name: offer
  namespace: ms
spec:
  selector:
    app: "offer"
  type: ClusterIP
  ports:
  - name: offer
    port: 1001
    targetPort: 1001
```

```
kubectl apply -f ./offers-microservice-spring-boot/kube.yaml
```

```
kubectl get all
```

Cart

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  "cart"
  namespace: ms
  labels:
    app:  "cart"
spec:
  selector:
    matchLabels:
      app: "cart"
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app:  "cart"
    spec:
      containers:
      - name:  "cart"
        image:  meruyerts/cart
        ports:
        - containerPort:  1004
          name:  "cart"
        
---

apiVersion: v1
kind: Service
metadata:
  name: cart
  namespace: ms
spec:
  selector:
    app: cart
  type: ClusterIP
  ports:
  - name: "cart"
    port: 1004
    targetPort: 1004
```

```
kubectl apply -f ./cart-microservice-nodejs/kube.yaml
```

```
kubectl get all
```

Wishlist

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  "wishlist"
  namespace: ms
  labels:
    app:  "wishlist"
spec:
  selector:
    matchLabels:
      app: "wishlist"
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app:  "wishlist"
    spec:
      containers:
      - name:  "wishlist"
        image:  meruyerts/wishlist
        ports:
        - containerPort:  5000
          name:  "wishlist"

---

apiVersion: v1
kind: Service
metadata:
  name: wishlist
  namespace: ms
spec:
  selector:
    app: "wishlist"
  type: ClusterIP
  ports:
  - name: wishlist
    port: 1003
    targetPort: 5000
```

```
kubectl apply -f ./wishlist-microservice-python/kube.yaml
```



