# Jenkins Exam Notes

See Documentation.pdf for more details.

--- 

##### Link to GitHub Repo

https://github.com/baris1892/datascientest-jenkins_devops_exams


---

#### Dockerhub Images

- https://hub.docker.com/repository/docker/baris1892/datascientest-cast-service/general
- https://hub.docker.com/repository/docker/baris1892/datascientest-movie-service/general

---

##### Create Namespaces

```
kubectl create namespace dev
kubectl create namespace qa
kubectl create namespace staging
kubectl create namespace prod
```

#### Preview Helm Chart

```
helm template cast-service ./charts -f ./charts/values-cast-service.yaml

# or alternatively
helm upgrade --install cast-service ./charts   -f ./charts/values-cast-service.yaml   --namespace dev   --dry-run   --debug 
```

##### Deploy using Helm Chart

```
# deploy cast service
helm upgrade --install cast-service ./charts \
  -f ./charts/values-cast-service.yaml \
  --namespace dev

# deploy movie service
helm upgrade --install movie-service ./charts \
  -f ./charts/values-movie-service.yaml \
  --namespace dev

# check resources
kubectl -n dev get all 
kubectl -n dev get pods
kubectl -n dev get svc
kubectl -n dev get statefulsets
```

