# k8s-test-local-deployment
For deploying Sample 3tier app in https://github.com/andes2020/k8s-test-docker-push

## Deploy DB
minikube start
minikube kubectl -- apply -f db.yaml

## Test psql deployment is working
minikube kubectl -- kubectl run postgresql-postgressql-client --rm --tty -i --restart='Never' --namespace default --image bitnami/postgresql --env="PGPASSWORD=admin123" --command -- psql -h <ClusterIP> -U postgresadmin --password -p 5432 postgresdb