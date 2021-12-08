# Kubernetes - ConfigMaps and Secrets

## Step-01: Introduction
- ConfigMaps are use to pass additional configurations needed to orchestarte containers inclusing environmental variables. Examples is hostname or password.
- ConfigMaps pass values in plain text key:value pairs
- Kubernetes Secrets let you store and manage sensitive information, such as passwords, OAuth tokens, and ssh keys. 
- Storing confidential information in a Secret is safer and more flexible than putting it directly in a Pod definition or in a container image. 

## Step-02: Create Secret for mongo DB Password
### 
```
# Mac
echo -n 'dbpassword11' | base64

# URL: https://www.base64encode.org
```
### Create Kubernetes Secrets manifest
```yml
kind: ConfigMap 
apiVersion: v1 
metadata:
  name: mongo-configmap 
data:
  # Configuration values can be set as key-value properties
  db-password: devdb@123
  db-hostname: devdb
 ---
apiVersion: v1
kind: Secret
metadata:
  name: mongo-db-password
#type: Opaque means that from kubernetes's point of view the contents of this Secret is unstructured.
#It can contain arbitrary key-value pairs. 
type: Opaque
data:
  # Output of "echo -n 'devdb@123' | base64"
  db-password: ZGV2ZGJAMTIz
```
## Step-03: Update secret in mongodb Deployment for DB Password
```yml
          env:
            - name: MONGO_DB_USERNAME
              valueFrom: 
                configMapKeyRef 
                  name: mongo-configmap 
                  key: db-username
            - name: MONGO_INITDB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongo-db-password
                  key: db-password
```

## Step-04: Update secret in springapp Deployment
- UMS means springapp User Management Microservice
```yml
            - name: MONGO_DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-db-password
                  key: db-password
```

## Step-05: Create & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:nodePort
```

## Step-06: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/

# List Pods
kubectl get pods

# Verify sc, pvc, pv
kubectl get sc,pvc,pv
```
