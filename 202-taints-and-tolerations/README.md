# Taints and Tolerations

**This exercise should only be done once per cluster.**

## Preparation

* Before you begin with the actual exercise please make sure to follow these steps to work in your own environment:

```shell
kubectl create namespace <YOURNAME>
kubectl config set-context --current --namespace="${YOURNAME}"
```

* Clone this repository to your working station and change into the directory for this exercise

---

## Exercise

* Taint a node

  ```shell
  kubectl taint node <NODENAME> workshop=true:NoExecute
  ```

* Inspect the node and pods running on it

  ```shell
  kubectl describe node <NODENAME>
  kubectl get pods -A -o wide | grep "<NODENAME>"
  ```

* Spin up a deployment with a toleration

  ```shell
  kubectl apply -f deployment.yaml
  ```

* Note that the pods *might* be scheduled on the node now but they can still run on any node

* Use the `nodeName` spec to force pods on the tainted node.
  (Normally one would use nodeAffinity here but to speed things up we use `nodeName` directly)

  ```shell
  # change the value of .spec.template.spec.nodeName
  vi deployment-nodeSelector.yaml
  ```

  ```shell
  kubectl apply -f deployment-nodeSelector.yaml 
  ```

* Now watch where the pods are scheduled

* Make sure to remove the taint after you are finished

  ```shell
  kubectl taint node <NODENAME> workshop=true:NoExecute-
  ```

---

### Clean up

* Lets tear down everything

```shell
kubectl delete -f deployment.yaml
```
