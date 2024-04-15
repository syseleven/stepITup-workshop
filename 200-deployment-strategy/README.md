# Deployment Strategy

## Preparation

* Before you begin with the actual exercise please make sure to follow these steps to work in your own environment:

```shell
kubectl create namespace <YOURNAME>
kubectl config set-context --current --namespace="${YOURNAME}"
```

* Clone this repository to your working station and change into the directory for the following exercises

---

## Exercise

## Part 1 - Recreate

* Create a deployment with nginx 1.22.0 pods and a strategy of Recreate

  ```shell
  kubectl -n ${YOURNAME} apply -f deploy-recreate.yaml
  ```

* Observe the deployment Events

  ```shell
  kubectl -n ${YOURNAME} describe deployment recreate
  ```

  ```text
  # Result:
  Scaled up replica set recreate-xxxxxxxx to 4
  ```

* Update the container image to 1.23.0

  ```shell
  kubectl -n ${YOURNAME} set image deployment/recreate nginx=nginx:1.23.0
  ```

* Observe the deployment events

  ```shell
  kubectl -n ${YOURNAME} describe deployment recreate
  ```

  ```text
  # Result:
  Scaled up replica set recreate-xxxxxxxx to 4
  Scaled down replica set recreate-xxxxxxxx to 0
  Scaled up replica set recreate-yyyyyyyy to 4
  ```

### Conclusion

A deployment strategy of `Recreate` causes a downtime for the application.

---

## Part 2 - RollingUpdate

* Create a deployment with nginx 1.22.0 pods and a strategy of RollingUpdate

  ```shell
  kubectl -n ${YOURNAME} apply -f deploy-rollingupdate.yaml
  ```

* Observe the deployment Events

  ```shell
  kubectl -n ${YOURNAME} describe deployment rollingupdate
  ```

  ```text
  # Result:
  Scaled up replica set rollingupdate-xxxxxxxx to 4
  ```

* Update the container image to 1.23.0

  ```shell
  kubectl -n ${YOURNAME} set image deployment/rollingupdate nginx=nginx:1.23.0
  ```

* Observe the deployment events

  ```shell
  kubectl -n ${YOURNAME} describe deployment rollingupdate
  ```

  ```text
  # Result:
  Scaled up replica set rollingupdate-xxxxxxxx to 4
  Scaled up replica set rollingupdate-yyyyyyyy to 4
  Scaled down replica set rollingupdate-xxxxxxxx to 1
  Scaled down replica set rollingupdate-xxxxxxxx to 0
  ```

### Clean up

* Delete the deployments before proceeding with the next hands-on session:

  ```shell
  kubectl -n ${YOURNAME} delete deployment recreate rollingupdate
  ```

---

### Conclusion

A deployment strategy of `RollingUpdate` causes no downtime for the application and is the basis
for production ready update rollouts such as Blue/Green- and Canary-Deployments.

`RollingUpdate` is the default for deployments.
