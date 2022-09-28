# Kubectl cheat sheet

## Syntax
All commands use the following structure in the CLI—and keeping each component in order is critical:

```
kubectl [command] [TYPE] [NAME] [flags]
```

What does each piece mean?

### Command
The command portion describes what sort of operation you want to perform—namely `create`, `describe`, `get`, `apply`, and `delete`. You can perform these commands on one or more resources, since it’s possible to designate multiple resources within a single command.

- `create` generates new resources from files or standard input devices
- `describe` retrieves details about a resource or resource group
- `get` fetches cluster data from various sources
- `delete` erases resources as required
- `apply` pushes changes based on your configuration files

### TYPE
The TYPE attribute describes the kind of resource kubectl is targeting. What are these resources, exactly? Things like pods, services, daemonsets, deployments, replicasets, statefulsets, Kubernetes jobs, and cron jobs are critical components within the Kubernetes system. Because they impact how your deployments behave, it makes sense that you’d want to modify them.

### NAME
The NAME field is case sensitive and specifies the name of the resource in question. Tacking a name onto a command restricts that command to that sole resource. Omitting names from your command will pull details from all resources, like pods or jobs.

### Flags
Finally, flags help denote special options or requests made to a certain resource. They work as modifiers that override any default values or environmental variables.

## Kubectl Get

`kubectl get` offers a range of output formats:

- `-o wide` just adds more information (which is dependent on the type of objects being queried).
- `-o yaml` and `-o json` output the complete current state of the object (and thus usually includes more information than the original manifest files).
- `-o jsonpath` allows you to select the information you want out of the full JSON of the -o json option using the jsonpath notation.
- `-o go-template` allows you to apply Go templates for more advanced features.

Here are some examples of commonly used commands:

List all pods in the default namespace (in this case, there are no pods in the default namespace):
```
$ kubectl get pod
No resources found in default namespace.
```

Get more information about a given pod:
```
$ kubectl -n mynamespace get po mypod-0 -o wide
NAME      READY   STATUS    RESTARTS   AGE    IP               NODE        NOMINATED NODE   READINESS GATES
mypod-0   2/2     Running   0          4d3h   192.168.181.98   node1.lan   [none]           [none]
```

Get the full state in YAML of the same pod as above:

```
$ kubectl -n mynamespace get pods/mypod -o yaml
apiVersion: v1
kind: Pod
metadata:
[ --- snip --- ]
```

To get the services in the default namespace:

```
$ kubectl get svc
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
kubernetes       ClusterIP   10.100.0.1      [none]        443/TCP    24d
mysql            ClusterIP   10.100.78.115   [none]        3306/TCP   22d
mysql-headless   ClusterIP   None            [none]        3306/TCP   22d
```

## Kubectl Describe 
To retrieve a lot of information about Kubernetes objects that are not shown by kubectl get, use kubectl describe:

```
$ kubectl -n mynamespace describe pods/mypod-0
Name:         mypod-0
Namespace:    mynamespace
[ --- snip --- ]
Containers:
  myapp:
    Container ID:   docker://9958565f45e7ddd27a5a7ca88254d3d27c162bb7612f0bb4c04781f154b33fd9
    Image:          myapp:3
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 26 Aug 2021 13:31:13 +0100
    Ready:          True
[ --- snip --- ]
Events:                      [none] 
```

```
$ kubectl describe nodes
Name:               minikube
Roles:              control-plane,master
[ --- snip --- ]
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Mon, 11 Oct 2021 07:36:08 +0545   Sun, 29 Aug 2021 22:00:50 +0545   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure     False   Mon, 11 Oct 2021 07:36:08 +0545   Sun, 29 Aug 2021 22:00:50 +0545   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Mon, 11 Oct 2021 07:36:08 +0545   Sun, 29 Aug 2021 22:00:50 +0545   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready            True    Mon, 11 Oct 2021 07:36:08 +0545   Sun, 29 Aug 2021 22:01:03 +0545   KubeletReady                 kubelet is posting ready status
[ --- snip --- ]
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                790m (39%)  6 (300%)
  memory             390Mi (5%)  3242Mi (41%)
  ephemeral-storage  100Mi (0%)  0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:              
```

To get the logs from a container:

```
$ kubectl logs mypod-0 -c myapp
```

## Interacting With Pods
Nothing beats getting a shell to a running pod! Here’s how to do it (you can skip the -c argument if you have only one container running inside the pod):

```
$ kubectl -n NS exec -it POD -c CONTAINER -- sh
```

You can quickly create a link between a given port number on a given pod running inside the Kubernetes cluster and your computer (this is called port forwarding). Here is how to do it, assuming your pod exposes port 8080:
```
$ kubectl port-forward MYPOD 8888:8080
```

You can then open up port 8888 on your local computer and that will forward the traffic to the pod MYPOD on its port 8080. This is very useful to do a quick check on your pod to verify that it looks OK
