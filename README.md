# k8s-test-local-deployment
For deploying Sample 3tier app in https://github.com/andes2020/k8s-test-docker-push

CI build of docker images has been done 
[web and api node server docker build and push](https://github.com/andes2020/k8s-test-docker-push/actions)

## Deploy DB with postgresql and persistence volume
```
minikube start
minikube kubectl -- apply -f db.yaml
```
https://platform9.com/blog/tutorial-dynamic-provisioning-of-persistent-storage-in-kubernetes-with-minikube/

## Test psql deployment is working
```
minikube kubectl -- kubectl run postgresql-postgressql-client --rm --tty -i --restart='Never' --namespace default --image bitnami/postgresql --env="PGPASSWORD=admin123" --command -- psql -h <ClusterIP> -U postgresadmin --password -p 5432 postgresdb
```

## Deploy api
Internal pod to postgres sql connection doesn't seem to work with Minikube.
Not sure why...

Referencing:
1. https://stackoverflow.com/questions/63591762/connect-to-postgresql-from-inside-kubernetes-cluster
2. https://learnk8s.io/deploying-nodejs-kubernetes
3. https://github.com/fgallina/bookapi (This example is using minikube with but it has to expose the postgres db host as nodeport)

In reality, cloud should be easily done with internal dns resolution
