# Affinity

## podAntiAffinity

## Preparation

* Before you begin with the actual exercise please make sure to follow these steps to work in your own environment:

```shell
kubectl create namespace <YOURNAME>
kubectl config set-context --current --namespace="${YOURNAME}"
```

* Clone this repository to your working station and change into the directory for the following exercises

---

## Exercise

### Part 1 - antiAffinity required

* Create a deployment of which all pods are scheduled on different nodes.

  ```shell
  kubectl apply -f deploy-antiaffinity-required.yaml 
  ```

* List the pods with node names. Verify distribution.

  ```shell
  kubectl get pods -l app=nginx -o wide
  ```

* We have a certain amount of worker nodes and now scale up the deployment until there are more pods than nodes available

  ```shell
  # Determine the number of worker nodes
  NODECOUNT=`kubectl get no --no-headers=true | wc -l | tr -d " "`
  
  # Scale up the deployment
  kubectl scale deployment nginx --replicas=`expr $NODECOUNT + 2`
  
  # Verify the distribution of pods
  kubectl get pods -l app=nginx -o wide
  ```

* Expected result:
  * some new pods remain in state `Pending` since they cannot be scheduled due to their
    restriction "not to be coupled with each other on the same node"
  * even if the topologyKey would require them to do so
  * This happens because the antiAffinity is validated in the moment of scheduling

* Get events from a `pending` pod:

  ```shell
  kubectl describe po <POD-NAME>
  ```

### Clean up

* Delete the deployment

  ```shell
  kubectl delete -f deploy-antiaffinity-required.yaml
  ```

---

### Part 2 - antiAffinity preferred

* Create a new deployment

  ```shell
  kubectl apply -f deploy-antiaffinity-preferred.yaml
  ```

* List the pods with node names. Verify distribution.

  ```shell
  kubectl get pods -l app=nginx -o wide
  ```

* We have a certain amount of worker nodes and now scale up the deployment until there are more pods than nodes available

  ```shell
  # Determine the number of worker nodes
  NODECOUNT=`kubectl get no --no-headers=true | wc -l`
  
  # Scale up the deployment
  kubectl scale deployment nginx --replicas=`expr $NODECOUNT + 2`
  
  # Verify the distribution of pods
  kubectl get pods -l app=nginx -o wide
  ```

* Expected result:
  * The new pods can now be scheduled
  * they are scheduled on nodes where there are already pods of the same type running
  * This is possible because the antiAffinity type `preferred` does not enforce a strict
    distribution in the moment of scheduling

### Clean up

* Delete the deployment again

```shell
kubectl delete -f deploy-antiaffinity-required.yaml
```

---

### Conclusion

You can now tell the difference between required and preferred (anti)Affinity
and use it to distribute pods to nodes.

Also there are settings such as:
- nodeAffinity
- podAffinity
- podAntiAffinity
