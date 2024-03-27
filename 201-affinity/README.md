# Affinity

## Preparation

* Before you begin with the actual exercise please make sure to follow these steps to work in your own environment:

```shell
kubectl create namespace <YOURNAME>
kubectl config set-context --current --namespace="${YOURNAME}"
```

* Clone this repository to your working station and change into the directory for this exercise

---

## Exercise

* Create a deployment using node affinity

  ```shell
  kubectl apply -f deployment.yaml
  ```

* This deployment uses the label selector `kubernetes.io/os=linux`.
  So inspect the node labels:

  ```shell
  kubectl get nodes --show-labels
  ```

* View the pods being scheduled to the nodes

  ```shell
  kubectl get pods -o wide
  ```

* Select a node and give at a new unique label

  ```shell
  kubectl label node <NODE> my-label=<MY-NAME>
  ```

* Edit the deployment to use the new label for affinity

  ```shell
  # change the key and value to match your new label
  kubectl edit deployment affinity-deployment
  ...
  - matchExpressions:
  - key: my-label
    operator: In
    values:
    - <MY-NAME>
  ...
  ```

* Watch where pods are being scheduled now

   ```shell
   kubectl get pods -o wide
   ```

---

### Clean up

* Lets tear down everything

```shell
kubectl delete -f deployment.yaml
```
