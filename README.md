# k8s-test-local-deployment
=======
For deploying Sample 3tier app in https://github.com/andes2020/k8s-test-docker-push

- CI build of docker images has been done 
[web and api node server docker build and push](https://github.com/andes2020/k8s-test-docker-push/actions)

Here is to demonstrate how to use minikube and kubernetes to deploy 
- 1 postgres database using postgres docker image --> db.yaml
- 2 custom nodejs application docker images --> api.yaml and app.yaml

I have not complete the app.yaml completely as API_HOST need to be exposed external IP/URL which minikube is not realistic to do so.

If you are using aws/gcp/azure or other public cloud, then you can do so.

## Deploy DB with postgresql and persistence volume
```
minikube start
minikube kubectl -- apply -f db.yaml
```
https://platform9.com/blog/tutorial-dynamic-provisioning-of-persistent-storage-in-kubernetes-with-minikube/

### Test psql deployment is working
```
minikube kubectl -- kubectl run postgresql-postgressql-client --rm --tty -i --restart='Never' --namespace default --image bitnami/postgresql --env="PGPASSWORD=admin123" --command -- psql -h <ClusterIP> -U postgresadmin --password -p 5432 postgresdb
```

## Deploy api
This will deploy the node api server into minikube with 3 pods.

```
minikube kubectl -- apply -f api.yaml
```

### Test it is working by 
1. Get a local minikube tunnel to access as if external ip exists in cloud
```
minikube service api-service --url 
```

2. Test api server connection
```
curl <Minikube Tunnel URL>/api/status
```

### Outcome
Result should be a Json response as following
```
[{"time":"2021-04-03T14:16:17.052Z"}]
```

[Minikube Loadbalancer resolution](https://stackoverflow.com/questions/44110876/kubernetes-service-external-ip-pending)


## Deploy the webapp (Won't do in minikube)

### Assumption (Prerequisite)
Assume the api server is exposed by external loadbalancer with IP/ URL. I Can't be bothered to use minikube. :/

### Configure API_HOST
You can then configure the API_HOST spec.containers[0].env env variable to deploy and run the webapp.
```
spec:
      containers:
        - name: k8s-test-node-api-server
          image: andeslam2019/k8s-test-node-web-app:latest
          imagePullPolicy: Always
          ports:
            - containerPort: 3000
          env:
            - name: PORT
              value: "3000"
            - name: API_HOST
              value: '<THIS SHOULD BE the expose API service loadbalancer IP or URL >' # Same as created from api.yaml service
```

Then run ```kubectl apply -f app.yaml```

You should then be able to access the web app (Front end) using the webapp loadbalancer service IP.

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

# Minikube Kubernetes debug
1. Use minikube dashboard to access kubernetes dashboard
2. Use ```kubectl get events```

