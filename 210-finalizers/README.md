# Finalizers and Owner Reference

## Preparation

* Before you begin with the actual exercise please make sure to follow these steps to work in your own environment:

  ```shell
  kubectl create ns <YOURNAME>
  kubectl config set-context --current --namespace=<YOURNAME>
  ```

* Clone this repository to your working station and change into the directory for this exercise

---

## Exercise 1

### Working with finalizers

* Create a configmap with a finalizer

  ```shell
  kubectl apply -f mymap.yaml
  ```

* Try to delete the configmap

  ```shell
  kubectl delete -f mymap.yaml
  ```

* Abort with `CTRL+C`

* Result: the resource gets a deletion timestamp but will not be removed.
* Verify the result:

  ```shell
  kubectl get -f mymap.yaml -o yaml
  ```
  
* Note the deletion information and finalizer

  ```yaml
  ...
  deletionGracePeriodSeconds: 0
  deletionTimestamp: "2024-04-15T10:59:16Z"
  finalizers:
    - kubernetes
  ...
  ```

### Patch the configmap resource

* Prepare the configmap to enable deletion by applying a patch action

    ```shell
    kubectl patch configmap/mymap \
    --type json \
    --patch='[ { "op": "remove", "path": "/metadata/finalizers" } ]'
    ```

* Verify the patch was successfully applied

  ```shell
  kubectl get -f mymap.yaml
  ```

* Expected result: `$ Error from server (NotFound): configmaps "mymap" not found`
  * After the patch was applied, the finalizer is removed and
  * the configmap resource immediately gets deleted.

---

## Exercise 2

### Working with owner references

* Create two configmap resources which are linked by an owner reference

  ```shell
  kubectl apply -f mymap-parent.yaml
  ```

* Obtain the `uid` of the parent configmap:

  ```shell
  CM_UID=$(kubectl get configmap mymap-parent -o jsonpath="{.metadata.uid}") 
  ```

* Create a child configmap which is linked to the parent resource by its `uid`

  ```shell
  cat <<EOF | kubectl create -f -
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: mymap-child
    ownerReferences:
    - apiVersion: v1
      kind: ConfigMap
      name: mymap-parent
      uid: $CM_UID
  EOF
  ```

* View the new child configmap

  ```shell
  kubectl get cm
  ```

* Remove the parent configmap:

  ```shell
  kubectl delete -f mymap-parent.yaml
  ```

* Verify the result:

  ```shell
  kubectl get configmaps
  ```

* Conclusion: Both configmaps are delete because of their reference
