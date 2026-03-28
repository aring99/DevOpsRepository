# Kubernetes Notes

Kubernetes is a wrapper for docker.
Docker images become containers when run

A pod, the smallest object in Kubernetes. Which is 1 or more containers. 1 per pod is the best.

We achieve a pod by wrapping a container, we achieve this using yaml files.

Pods then get wrapped inside Replica Sets. A replica set allows us to control the number of pods available.  

With docker we can have stateless applications, things that aren't stored anywhere.

Replica Sets then are wrapped inside Deployment. It allows us to Rollback and Rollforward with updates.

All of the above is defined in a yaml file.

Non-breaking space highlighter plugin helps when copying and pasting code

Play with Kubernetes (K8S): https://labs.play-with-k8s.com

Katakoda: https://www.katacoda.com

https://github.com/addamstj/kubernetes-course

Yaml stands for yet another markup language btw
Always 2 spaces.

Lists and dictionaries
  #this is a list, an array, a collection of records
shopping:
  - eggs
  - bread: 1
  - fruit: apple
 
key: value
#this is a dictionary, key value pair

Kubernetes has an API endpoint.

Kubernetes needs 4 things.
The kind, such as the pod
The api version. It needs that for the schemer, which is like a template. An API will use a schemer to validate and check what it should be receiving.
Metadata, details about the pod. Such as the name and labels.
Spec, is for specifications.

-Create a pod, api version v1 (so the schemer knows what to expect), with this name and label and with these specifications(this is what I want you to actually create).

Resource limits need to be defined. 

To create a pod, enter kubectl create -f yamlfilename.yaml
** container names should all be in lower case

 kubectl get pods to see pods

İf we need to make a change and we want to apply it on a running pod, kubectl create can return an error. For that reason, we use
 kubectl apply -f filename.yaml


Replica Sets vs ReplicationControllers

They both do the same thing. ReplicaSets is the newer one.

The replica number we give under specifications here is "desired state configuration", which means Kubernetes will always try to make that many replicas running.
If one pod crashes or we delete one, it will immediately create another pod

We have matchLabels, so we can attach the pods to the ReplicaSet, it will take all pods with the matching labels

We have the command, kubectl get replicaset

You can run on Mac "brew install watch", this will run kubectl get pods every 2 seconds

You can also run kubectl delete replicaset replicasetname

This also deletes all associated pods too.

Deployments, under strategy we have maxsurge (pods we are updating) and max unavailable. You can give an integer "1" or a percentage like 25% 

Resources, requests. It signifies the amount of resources needed as soon as the pod starts. 
LivenessProve and readinessProbe. LivenessProbe tests if the pod is alive, checks if it is running. ReadinessProbe, it won't define the pod as ready until it returns some form of message.

imagePullPolicy: every time a pod is deployed, always pull the image from the repository. Since the images are cached, there is other ways instead of pulling from the repository.
RestartPolicy: defines how the system should behave if a container fails.

*You can use rs for short of replicaset and deploy for deployment. A kubectl delete rs myreplicaset


Multicontainer pods: not best practice, but they can be used for adding proxies or healthchecks

You can use "command: [""],["-c"]" to give a command to the container.

You can use kubectl describe pod podname to see details about the pod, such as how many containers run inside it.

**You can use exec to a container in Kubernetes like in docker "kubectl exec -it podname -c container name command (for example -- /bin/bash)
Since most commands aren't installed, you can run apt-get update and other commands that might be needed. (Note that this will be lost when we restart the pod)


INIT containers

Sometimes we need to pull some information and we don't need it running all the time, for example certificates. Run a curl command to get it, return the result, then stop.
They run before the main container. A popular one is vault-sidekick


kubectl logs podname -c containername 


**Kubernetes Networking

Kubernetes has its own network range. 10.x.x.x this is from Kubernetes's internal network from inside the cluster. All nodes can talk to each other.

Each pod has a ClusterIp, this is only accessible inside the Kubernetes network

The above is for internal connections. For external connections there are the below options

Nodeport, LoadBalancer and ingress


The nodeport service exposes a port in the node for the pods inside that have a matching label with it. **NEVER use nodeport in production, they are very insecure.

LoadBalancer is logically similar, but with multiple nodes involved.

**Ingress. Ingress has a special type of pod that is at the front, it can be used together with nginx. The pod is called the Ingress Controller.
A service is created to link it to the Ingress Controller and a pod.

External Load balancers are expensive, no need for it with Ingress. It is easy to use and an endpoint can be exposed.


In Kubernetes, there are two important components that are used. Kube-Proxy and Kube-DNS. Both of them run on every single node. They are like a process.

Kube DNS keeps a record of all the different pod names, services, fully qualified domain names. Kube-Proxy manages all the networking.

Ingress can function like a router. Multiple domains, subdomains and many different configs on it. When setting up the service.yaml, the target port is the port of the container we want to connect to.
And the port itself is for the service

In ingress.yaml
"spec:
  rules:
  - host: mysite.local"
...
Means do "..." If traffic comes to mysite.local

We call this section an ingress resource.


Pod to pod communication. The cluster IPs, name and namespace. Namespace is a way to separate environments.
https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/

For namespaces, you run Namespace.yaml and also add a namespace to the metadata of a pod, while getting pods you also need to specify namespace with: kubectl get pods -n namespacename


Kubernetes Storage

Basic type of storage: something for certificates for example. Init containers were mentioned for the task, ideally, we want this data not in a container but a volume that any pod can access.

This can be achieved with a storage option emptydir. It is a volume that's accessible (for all containers inside a pod)and only available for the lifetime of a pod.

So our init container can mount this volume, pull down the certs, store them in this volume.

Then when the init container stops and is terminated, it will then become available to our main container. This volume is only available for the node it is running on.


Persistent Volumes and Persistent Volume Claims

Persistant Volumes, fix volumes, set size.

*important bit to remember.

We, the administrator, will create the EBS volume.

Then in Kubernetes, we'll create the two persistent volumes and then if anyone needs to use it,

developers and DevOps team, for example, they will create a persistent volume claim to say, I want

a chunk of that storage to use it.

Storage Class

Storage class, in essence is a bit like giving the persistent volume a label, except we call it a

storage class name.

Static versus dynamic storage classes.

Static mean we as the admin define a persistent volume and the storage class name; we set it all up. Dynamic on the other hand creates it for us.
---

---

The triple dashes allow us to put multiple Kubernetes objects that we want to create inside a single file.

Check the code folder "storage"


ConfigMaps


https://matthewpalmer.net/kubernetes-app-developer/articles/ultimate-configmap-guide-kubernetes.html

So the point of a config map is to store configuration files.

So rather than having it hard coded inside the Docker container, we can now have it decoupled or separated

away from being baked in.

if you want to turn the configmap (cm) into a yaml file, run kubectl get cm namehere -o yaml > namehere.yaml

When adding a config map to the container, you create it under volumes. You use its name and then you open "items:" to access the contents, the key value pair. The key is the name of the file and then 
When we provide the path, we are naming the output in a sense. Then mount the volume. There is a very important step here, common reason why cms don't work.

Without it, when you define the volume mount, the volume path gets overwritten. The thing that is being talked about is a "sub-path"

Find and use code

Useful commands:

Kubectl exec -it podname (if there are multiple containers, give the container name w) -c containername (to run the command add the following -- /bin/bash)

You can't exec into init containers, it shutdowns when the shift ends

Getting output from containers, kubectl logs podname

If you want to see the actual logs themselves, if they're in a specific path, you can use Kubectl exec to have a look. This works on both init containers and the normal containers, add dash C and pass in the container name if there are numerous containers.

"kubectl describe pod podname" is used alot. If you start running to issues and start seeing crash looping down it will tell you why at the bottom.

Also check the restart count and see if "ready = true" or not

You can pipe any kubectl object into yaml file, for example a service. Kubectl get service servicename -o yaml> name.yaml

Making changes on the fly: kubectl edit typeofobject(ex:service, deployment) nameobject

How to see cpu and memory usage in pods, nodes. Run minikube addons enable metrics-server

Then you can run kubectl top pod (you can add --containers for container resource percentage)

You can run minikube dashboard to see a dashboard in browser showing the status

For running maintenance on nods, you can use kubectl drain nodename

This will make the pods inside go and run in a different node and the node we drained will be unavailable, so we can troubleshoot it. Then, you can run kubectl undcordon nodename

To have our pods run on specific nodes, we add "nodeSeelector" to the yaml file and will function like the labels do. Taint does the opposite, don't deploy to that node. Toleration adds exception to it.


HPA's and VPA's, horizontal and vertical pod autoscalers. hpa adds extra containers if a percentage of load exists on containers.

Vpa's increases the pod's size to handle the task, it deletes the previous smaller pod

Metrics: cpu and memory. When checking the percentage condition (70% cpu usage), HPA checks the !average! Of all containers for that percentage. Even if a policy detects and adds an extra container,
All traffic is going to stay/be at the other containers. Even if the pods are crashing.

That is why set up a custom metrics api!! Numbers of requests etc. don't use HPA in java with standard metrics.. You can use rate of the number of requests coming in. You can use Prometheus as a custom metrics api!!

Hpa checks the resources and limits under it. To not run into problems with multi containers, make sure this exists for ever container and have the limit

