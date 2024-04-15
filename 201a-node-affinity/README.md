# Affinity

## Preparation

* Before you begin with the actual exercise please make sure to follow these steps to work in your own environment:

```shell
kubectl create namespace <YOURNAME>
kubectl config set-context --current --namespace="${YOURNAME}"
```

* Clone this repository to your working station and change into the directory for this exercise

---

## Exercise 1 - Node Affinity

* Create a deployment using node affinity

  ```shell
  kubectl apply -f deployment-node-affinity.yaml
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
  kubectl edit deployment deployment-node-affinity
  ```

  ```yaml
  # change the key and value to match your new label
  ...
  - matchExpressions:
    - key: my-label # <-- adjust here
      operator: In
      values:
      - <MY-NAME> # <-- adjust here
  ...
  ```

* Display the node where pods are now being scheduled on

   ```shell
   kubectl get pods -o wide
   ```

### Clean up

* Lets tear down everything

  ```shell
  kubectl delete -f deployment-node-affinity.yaml
  ```

---

## Exercise 2 - Pod Anti Affinity

