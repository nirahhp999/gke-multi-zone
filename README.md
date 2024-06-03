# gke-multi-zone


```
The provided StatefulSet configuration aims to distribute the MongoDB pods across the specified zones (us-central1-a, us-central1-b, us-central1-c). However, the actual deployment across the nodes depends on several factors, including the availability of nodes in those zones and Kubernetes scheduling decisions.
```

***Understanding the Configuration***
```
Node Affinity:

The nodeAffinity rule specifies that MongoDB pods should be scheduled on nodes within the zones us-central1-a, us-central1-b, and us-central1-c.
Pod Anti-Affinity:

The podAntiAffinity rule specifies that MongoDB pods should be spread across different zones to ensure high availability. This is achieved by setting topologyKey to topology.kubernetes.io/zone.
```
***Expected Behavior***
```
With the given configuration, Kubernetes will try to distribute the MongoDB pods across nodes in the specified zones. Here's how it works:

Pod 1 might be scheduled on a node in us-central1-a.
Pod 2 might be scheduled on a node in us-central1-b.
Pod 3 might be scheduled on a node in us-central1-c.
If Kubernetes has nodes available in all the specified zones (us-central1-a, us-central1-b, us-central1-c), it will try to place each MongoDB pod in a different zone. This distribution improves fault tolerance and high availability.
```
***Verifying the Deployment***
After deploying the StatefulSet, you can verify where the pods are running using the following command:
```
kubectl get pods -o wide
```
Example Output
```
$ kubectl get pods -o wide
NAME      READY   STATUS    RESTARTS   AGE   IP            NODE                             NOMINATED NODE   READINESS GATES
mongo-0   1/1     Running   0          10m   10.60.1.1     gke-cluster-1-node-1-us-central1-a   <none>           <none>
mongo-1   1/1     Running   0          9m    10.60.2.2     gke-cluster-1-node-2-us-central1-b   <none>           <none>
mongo-2   1/1     Running   0          8m    10.60.3.3     gke-cluster-1-node-3-us-central1-c   <none>           <none>
```
***Considerations***
```
Node Availability: Ensure that you have nodes available in the specified zones. If nodes are not available in one of the zones, Kubernetes may place multiple pods in the same zone or fail to schedule some pods.
Node Resources: Make sure that the nodes have enough resources (CPU, memory, storage) to accommodate the MongoDB pods.
Node Labels: Verify that the nodes are correctly labeled with topology.kubernetes.io/zone.
If you need more control over the placement, consider additional scheduling constraints or manually ensuring node distribution.
```

***Applying the Configuration***
To apply the provided StatefulSet configuration, save it as mongo-statefulset.yaml and use kubectl to apply it:

```
kubectl apply -f mongo-statefulset.yaml
```