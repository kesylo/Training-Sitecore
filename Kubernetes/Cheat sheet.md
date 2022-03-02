# Kubernetes cheat sheet 

### Definitions

**Node**: (Worker node) Physical or virual machine on which kubernetes is installed and on which containers will be launched
**Cluster**: Set of nodes
**Master (Node)**: Just a node responsable for administration and orchestration of the worker nodes
**Pod**: Single instance of an app. Containers are not directly running in nodes. They are encapsulated in Pod object. (Smallest k8s object). 1 Container img = 1 POD
**K8s Components**:
    - **Etcd**: Key values storage to manage de cluster. has infos for all nodes in cluster. Installed on Master node only
    - **Scheduler**: Distribution of workload and containers accross nodes. Installed on Master node only
    - **Controller**: Whatches container and spins new onees if needed. Installed on Master node only
    - **Container runtime**: software used to run containers (docker)
    - **Kubelet**: Ageent running each node responsible to check the containers are running as excpected. Installed on Worker nodes only
    - **Api server**: Interface to talk to the cluster. Installed on Master node only
  
**Replication controller**: Ensure we have the amount of pods running as per config. If one fails, it respins. Now its called Replicaset. the difference applies in the ***selector*** spec which is required in replicaset and not replica controller

**K8s Deployment**: Used to update the environment in rolling manner (Handle changes in the env). Any changes of version, env variables, maintenance is done via the deployment. it encapsulate the replicaset. this means, creating a deployment create a replicaset and the replicaseet creates its pods. all based on the config

### Cheat sheet

- List all k8s object in cluster: 
    ```
    kubectl get all
    ```  
- Create/update/delete any k8s object from definition YAML: 
    ```
    kubectl create/apply/replace/delete -f pod-definition.yml
    ```

#### k8s Pods
- List pods: 
    ```
    Basic: kubectl get pods
    WIth more details: kubectl get pods -o wide
    ```  
- Deploy a pod (Nginx): 
    ```
    kubectl run nginx --image=nginx
    kubectl describe pod/replicaSet k8s_object_name
    kubectl get pods -o wide
    ``` 

#### k8s Replicasets
- Get replicasets: 
    ```
    kubectl get replicasets
    ```
- Scale replicaset: 
    ```
    kubectl scale --replicas=6 -f pod-definition.yml
    ```
#### k8s Deployments
- Update/rollback deployments: creating a deployment creates a rollout and a revision increase. this is like version control to rollback. Under the hood, k8s, creates a new repSet and spin pods there. at the same time, it delete the ones from the previous repSet. this is how its rolling update
    ```
    kubectl apply -f deployment-definition.yml (triggers rollout + revision)
    ```
- See status of deployment rollout: 
    ```
    kubectl rollout status deployment/name
    ```
- See rollout history: 
    ```
    kubectl rollout history deployment/name
    ```
- Undo rollout (revert): 
    ```
    kubectl rollout undo deployment/name
    ```
    
![alt text](https://raw.githubusercontent.com/kesylo/Trainings/master/Images/vison.png)

#### k8s Networking

In K8s, IP are assigned to each pod at their creation. This is an internal IP and they change on pod creation.

##### 1. Services
K8s services enables communication between group of pods. eg: a group of frontend pods want to talk to a group of backend pods and a the group of backend pods talking to the group of DB pods.

It also listen to a port on the node and forward incoming requests to the desired pod in the node (This setup is used to give external access to your app).

***Services types:***
- **NodePort**: As descibed above, this service is used to listen to a port number on the node and redirect external traffic to internal pods
- **ClusterIp**: Creates a virtual IP in the node to enable communication between group of services internally (Backend - Frontend - DB)
- **LoadBalancer**: Distribute external traffic to a group of pods depending on the load


### Ressources

- Install minikube: 
    ```
    https://minikube.sigs.k8s.io/docs/start/
    minikube dashboard
    ```
