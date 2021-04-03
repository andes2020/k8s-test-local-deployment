# k8s-test-local-deployment
For deploying Sample 3tier app in https://github.com/andes2020/k8s-test-docker-push

Start minikube first
```
minikube start
```
## Deploy DB
```
minikube kubectl -- apply -f db.yaml
```

## Test psql deployment is working
```
minikube kubectl -- kubectl run postgresql-postgressql-client --rm --tty -i --restart='Never' --namespace default --image bitnami/postgresql --env="PGPASSWORD=admin123" --command -- psql -h <ClusterIP> -U postgresadmin --password -p 5432 postgresdb
```

## Deploy api
This will deploy the node api server into minikube with 3 pods.

```
minikube kubectl -- apply -f db.yaml
```

Test it is working by 
1. Get a local minikube tunnel to access as if external ip exists in cloud
```
minikube service api-service --url 
```

2. Test api server connection
```
curl <Minikube Tunnel URL>/api/status
```

Outcome should be a Json response as following
```
[{"time":"2021-04-03T14:16:17.052Z"}]
```

# Scale up
Simply go to each yaml to upate Deployment ```spec.replicas```

Example
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
    ....
```

Then, apply yaml using ```kubectl apply -f <YAML file>```
