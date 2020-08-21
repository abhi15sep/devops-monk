---
title: Kubernetes practice questions for CKAD exam ?
author: Abhay
type: post
date: 2020-07-19T02:05:05+00:00
url: /2020/07/kubernetes-practice-CKAD-Exam/
blogger_permalink:
  - /2020/07/kubernetes-practice-CKAD-Exam.html
post_views_count:
  - 5
categories:
  - Devops
tags:
  - CKAD
  - Orchestration
  - Devops
---

Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications. The CNCF/Linux Foundation offers this performance-based exam which targets the developer aspect of kubernetes skills such as deploying apps, configuring apps, rolling out the application, creating persistent volumes, etc.

Since this exam is performance-based rather than just multiple choice questions just knowing the concepts are not enough, we need a lot of practice before the exam. This article helps you understand, practice and get you ready for the exam.

### Core Concepts (13%)

Practice questions based on these concepts:

- Understand Kubernetes API Primitives
- Create and Configure Basic Pods

**1. List all the namespaces in the cluster**
{{< highlight java >}}
kubectl get namespaces
kubectl get ns
{{< / highlight >}}

**2. List all the pods in all namespaces**
{{< highlight java >}}
kubectl get po --all-namespaces
{{< / highlight >}}

**3. List all the pods in the particular namespace**
{{< highlight java >}}
kubectl get po -n <namespace name>
{{< / highlight >}}

**4. List all the services in the particular namespace**
{{< highlight java >}}
kubectl get svc -n <namespace name>
{{< / highlight >}}

**5. List all the pods showing name and namespace with a json path expression**
{{< highlight java >}}
kubectl get pods -o=jsonpath="{.items[\*]['metadata.name', 'metadata.namespace']}"
{{< / highlight >}}

**6. Create an nginx pod in a default namespace and verify the pod running**
{{< highlight java >}}
// creating a pod
kubectl run nginx --image=nginx --restart=Never
// List the pod
kubectl get po
{{< / highlight >}}

**7. Create the same nginx pod with a yaml file**
{{< highlight yml >}}
// get the yaml file with --dry-run flag
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-pod.yaml

// cat nginx-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

// create a pod
kubectl create -f nginx-pod.yaml
{{< / highlight >}}

**8. Output the yaml file of the pod you just created**
{{< highlight java >}}
kubectl get po nginx -o yaml
{{< / highlight >}}

**9. Output the yaml file of the pod you just created without the cluster-specific information**
{{< highlight java >}}
kubectl get po nginx -o yaml --export
{{< / highlight >}}

**10. Get the complete details of the pod you just created**
{{< highlight java >}}
kubectl describe pod nginx
{{< / highlight >}}

**11. Delete the pod you just created**
{{< highlight java >}}
kubectl delete po nginx
kubectl delete -f nginx-pod.yaml
{{< / highlight >}}

**12. Delete the pod you just created without any delay (force delete)**
{{< highlight java >}}
kubectl delete po nginx --grace-period=0 --force
{{< / highlight >}}

**13. Create the nginx pod with version 1.17.4 and expose it on port 80**
{{< highlight java >}}
kubectl run nginx --image=nginx:1.17.4 --restart=Never --port=80
{{< / highlight >}}

**14. Change the Image version to 1.15-alpine for the pod you just created and verify the image version is updated**
{{< highlight java >}}
kubectl set image pod/nginx nginx=nginx:1.15-alpine
kubectl describe po nginx
// another way it will open vi editor and change the version
kubeclt edit po nginx
kubectl describe po nginx
{{< / highlight >}}

**15. Change the Image version back to 1.17.1 for the pod you just updated and observe the changes**
{{< highlight java >}}
kubectl set image pod/nginx nginx=nginx:1.17.1
kubectl describe po nginx
kubectl get po nginx -w # watch it
{{< / highlight >}}

**16. Check the Image version without the describe command**
{{< highlight java >}}
kubectl get po nginx -o jsonpath='{.spec.containers[].image}{"\n"}'
{{< / highlight >}}

**17. Create the nginx pod and execute the simple shell on the pod**
{{< highlight java >}}
// creating a pod
kubectl run nginx --image=nginx --restart=Never
// exec into the pod
kubectl exec -it nginx /bin/sh
{{< / highlight >}}

**18. Get the IP Address of the pod you just created**
{{< highlight java >}}
kubectl get po nginx -o wide
{{< / highlight >}}

**19. Create a busybox pod and run command ls while creating it and check the logs**
{{< highlight java >}}
kubectl run busybox --image=busybox --restart=Never -- ls
kubectl logs busybox
{{< / highlight >}}

**20. If pod crashed check the previous logs of the pod**
{{< highlight java >}}
kubectl logs busybox -p
{{< / highlight >}}

**21. Create a busybox pod with command sleep 3600**
{{< highlight java >}}
kubectl run busybox --image=busybox --restart=Never -- /bin/sh -c "sleep 3600"
{{< / highlight >}}

**22. Check the connection of the nginx pod from the busybox pod**
{{< highlight java >}}
kubectl get po nginx -o wide
// check the connection
kubectl exec -it busybox -- wget -o- <IP Address>
{{< / highlight >}}

**23. Create a busybox pod and echo message ‘How are you’ and delete it manually**
{{< highlight java >}}
kubectl run busybox --image=nginx --restart=Never -it -- echo "How are you"
kubectl delete po busybox
{{< / highlight >}}

**24. Create a busybox pod and echo message ‘How are you’ and have it deleted immediately**
{{< highlight java >}}
// notice the --rm flag
kubectl run busybox --image=nginx --restart=Never -it --rm -- echo "How are you"
{{< / highlight >}}

**25. Create an nginx pod and list the pod with different levels of verbosity**
{{< highlight java >}}
// create a pod
kubectl run nginx --image=nginx --restart=Never --port=80
// List the pod with different verbosity
kubectl get po nginx --v=7
kubectl get po nginx --v=8
kubectl get po nginx --v=9
{{< / highlight >}}

**26. List the nginx pod with custom columns POD_NAME and POD_STATUS**
{{< highlight java >}}
kubectl get po -o=custom-columns="POD_NAME:.metadata.name, POD_STATUS:.status.containerStatuses[].state"
{{< / highlight >}}

**27. List all the pods sorted by name**
{{< highlight java >}}
kubectl get pods --sort-by=.metadata.name
{{< / highlight >}}

**28. List all the pods sorted by created timestamp**
{{< highlight java >}}
kubectl get pods--sort-by=.metadata.creationTimestamp
{{< / highlight >}}

### Multi-Container Pods (10%)

Practice questions based on these concepts

- Understand multi-container pod design patterns (eg: ambassador, adaptor, sidecar)

**29. Create a Pod with three busy box containers with commands “ls; sleep 3600;”, “echo Hello World; sleep 3600;” and “echo this is the third container; sleep 3600” respectively and check the status**
{{< highlight java >}}
// first create single container pod with dry run flag
kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml -- bin/sh -c "sleep 3600; ls" > multi-container.yaml
// edit the pod to following yaml and create it
kubectl create -f multi-container.yaml
kubectl get po busybox

//multi-container.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
    - args:
        - bin/sh
        - -c
        - ls; sleep 3600
      image: busybox
      name: busybox1
      resources: {}
    - args:
        - bin/sh
        - -c
        - echo Hello world; sleep 3600
      image: busybox
      name: busybox2
      resources: {}
    - args:
        - bin/sh
        - -c
        - echo this is third container; sleep 3600
      image: busybox
      name: busybox3
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

      {{< / highlight >}}

**30. Check the logs of each container that you just created**
{{< highlight java >}}
kubectl logs busybox -c busybox1
kubectl logs busybox -c busybox2
kubectl logs busybox -c busybox3
{{< / highlight >}}

**31. Check the previous logs of the second container busybox2 if any**
{{< highlight java >}}
kubectl logs busybox -c busybox2 --previous
{{< / highlight >}}
**32. Run command ls in the third container busybox3 of the above pod**
{{< highlight java >}}
kubectl exec busybox -c busybox3 -- ls
{{< / highlight >}}

**33. Show metrics of the above pod containers and puts them into the file.log and verify**
{{< highlight java >}}
kubectl top pod busybox --containers
// putting them into file
kubectl top pod busybox --containers > file.log
cat file.log
{{< / highlight >}}

**34. Create a Pod with main container busybox and which executes this “while true; do echo ‘Hi I am from Main container’ >> /var/log/index.html; sleep 5; done” and with sidecar container with nginx image which exposes on port 80. Use emptyDir Volume and mount this volume on path /var/log for busybox and on path /usr/share/nginx/html for nginx container. Verify both containers are running.**
{{<highlight yaml>}}
// create an initial yaml file with this
kubectl run multi-cont-pod --image=busbox --restart=Never --dry-run -o yaml > multi-container.yaml
// edit the yml as below and create it
kubectl create -f multi-container.yaml
kubectl get po multi-cont-pod

//multi-container.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: multi-cont-pod
  name: multi-cont-pod
spec:
  volumes:
    - name: var-logs
      emptyDir: {}
  containers:
    - image: busybox
      command: ["/bin/sh"]
      args:
        [
          "-c",
          "while true; do echo 'Hi I am from Main container' >> /var/log/index.html; sleep 5;done",
        ]
      name: main-container
      resources: {}
      volumeMounts:
        - name: var-logs
          mountPath: /var/log
    - image: nginx
      name: sidecar-container
      resources: {}
      ports:
        - containerPort: 80
      volumeMounts:
        - name: var-logs
          mountPath: /usr/share/nginx/html
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{</highlight>}}

**35. Exec into both containers and verify that main.txt exist and query the main.txt from sidecar container with curl localhost**
{{< highlight java >}}
// exec into main container
kubectl exec -it multi-cont-pod -c main-container -- sh
cat /var/log/main.txt
// exec into sidecar container
kubectl exec -it multi-cont-pod -c sidecar-container -- sh
cat /usr/share/nginx/html/index.html
// install curl and get default page
kubectl exec -it multi-cont-pod -c sidecar-container -- sh
apt-get update && apt-get install -y curl
curl localhost
{{< / highlight >}}

### Pod Design (20%)

Practice questions based on these concepts

- Understand how to use Labels, Selectors and Annotations
- Understand Deployments and how to perform rolling updates
- Understand Deployments and how to perform rollbacks
- Understand Jobs and CronJobs

**36. Get the pods with label information**
{{< highlight java >}}
kubectl get pods --show-labels
{{< / highlight >}}
**37. Create 5 nginx pods in which two of them is labeled env=prod and three of them is labeled env=dev**
{{< highlight java >}}
kubectl run nginx-dev1 --image=nginx --restart=Never --labels=env=dev
kubectl run nginx-dev2 --image=nginx --restart=Never --labels=env=dev
kubectl run nginx-dev3 --image=nginx --restart=Never --labels=env=dev
kubectl run nginx-prod1 --image=nginx --restart=Never --labels=env=prod
kubectl run nginx-prod2 --image=nginx --restart=Never --labels=env=prod
{{< / highlight >}}
**38. Verify all the pods are created with correct labels**
{{< highlight java >}}
kubeclt get pods --show-labels
{{< / highlight >}}
**39. Get the pods with label env=dev**
{{< highlight java >}}
kubectl get pods -l env=dev
{{< / highlight >}}
**40. Get the pods with label env=dev and also output the labels**
{{< highlight java >}}
kubectl get pods -l env=dev --show-labels
{{< / highlight >}}
**41. Get the pods with label env=prod**
{{< highlight java >}}
kubectl get pods -l env=prod
{{< / highlight >}}
**42. Get the pods with label env=prod and also output the labels**
{{< highlight java >}}
kubectl get pods -l env=prod --show-labels
{{< / highlight >}}
**43. Get the pods with label env**
{{< highlight java >}}
kubectl get pods -L env
{{< / highlight >}}
**44. Get the pods with labels env=dev and env=prod**
{{< highlight java >}}
kubectl get pods -l 'env in (dev,prod)'
{{< / highlight >}}
**45. Get the pods with labels env=dev and env=prod and output the labels as well**
{{< highlight java >}}
kubectl get pods -l 'env in (dev,prod)' --show-labels
{{< / highlight >}}
**46. Change the label for one of the pod to env=uat and list all the pods to verify**
{{< highlight java >}}
kubectl label pod/nginx-dev3 env=uat --overwrite
kubectl get pods --show-labels
{{< / highlight >}}
**47. Remove the labels for the pods that we created now and verify all the labels are removed**
{{< highlight java >}}
kubectl label pod nginx-dev{1..3} env-
kubectl label pod nginx-prod{1..2} env-
kubectl get po --show-labels
{{< / highlight >}}
**48. Let’s add the label app=nginx for all the pods and verify**
{{< highlight java >}}
kubectl label pod nginx-dev{1..3} app=nginx
kubectl label pod nginx-prod{1..2} app=nginx
kubectl get po --show-labels
{{< / highlight >}}
**49. Get all the nodes with labels (if using minikube you would get only master node)**
{{< highlight java >}}
kubectl get nodes --show-labels
{{< / highlight >}}
**50. Label the node (minikube if you are using) nodeName=nginxnode**
{{< highlight java >}}
kubectl label node minikube nodeName=nginxnode
{{< / highlight >}}
**51. Create a Pod that will be deployed on this node with the label nodeName=nginxnode**
{{<highlight yaml>}}
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > pod.yaml
// add the nodeSelector like below and create the pod
kubectl create -f pod.yaml

//pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  nodeSelector:
    nodeName: nginxnode
  containers:
    - image: nginx
      name: nginx
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{</highlight>}}

**52. Verify the pod that it is scheduled with the node selector**
{{< highlight java >}}
kubectl describe po nginx | grep Node-Selectors
{{< / highlight >}}

**53. Verify the pod nginx that we just created has this label**
{{< highlight java >}}
kubectl describe po nginx | grep Labels
{{< / highlight >}}

**54. Annotate the pods with name=webapp**
{{< highlight java >}}
kubectl annotate pod nginx-dev{1..3} name=webapp
kubectl annotate pod nginx-prod{1..2} name=webapp
{{< / highlight >}}

**55. Verify the pods that have been annotated correctly**
{{< highlight java >}}
kubectl describe po nginx-dev{1..3} | grep -i annotations
kubectl describe po nginx-prod{1..2} | grep -i annotations
{{< / highlight >}}

**56. Remove the annotations on the pods and verify**
{{< highlight java >}}
kubectl annotate pod nginx-dev{1..3} name-
kubectl annotate pod nginx-prod{1..2} name-
kubectl describe po nginx-dev{1..3} | grep -i annotations
kubectl describe po nginx-prod{1..2} | grep -i annotations
{{< / highlight >}}

**57. Remove all the pods that we created so far**
{{< highlight java >}}
kubectl delete po --all
{{< / highlight >}}

**58. Create a deployment called webapp with image nginx with 5 replicas**
{{< highlight java >}}
kubectl create deploy webapp --image=nginx --dry-run -o yaml > webapp.yaml
// change the replicas to 5 in the yaml and create it
kubectl create -f webapp.yaml

//webapp.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: webapp
  name: webapp
spec:
  replicas: 5
  selector:
    matchLabels:
      app: webapp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: webapp
    spec:
      containers:
        - image: nginx
          name: nginx
          resources: {}
status: {}
```

{{< / highlight >}}

**59. Get the deployment you just created with labels**
{{< highlight java >}}
kubectl get deploy webapp --show-labels
{{< / highlight >}}
**60. Output the yaml file of the deployment you just created**
{{< highlight java >}}
kubectl get deploy webapp -o yaml
{{< / highlight >}}
**61. Get the pods of this deployment**
{{< highlight java >}}
// get the label of the deployment
kubectl get deploy --show-labels
// get the pods with that label
kubectl get pods -l app=webapp
{{< / highlight >}}
**62. Scale the deployment from 5 replicas to 20 replicas and verify**
{{< highlight java >}}
kubectl scale deploy webapp --replicas=20
kubectl get po -l app=webapp
{{< / highlight >}}
**63. Get the deployment rollout status**
{{< highlight java >}}
kubectl rollout status deploy webapp
{{< / highlight >}}
**64. Get the replicaset that created with this deployment**
{{< highlight java >}}
kubectl get rs -l app=webapp
{{< / highlight >}}
**65. Get the yaml of the replicaset and pods of this deployment**
{{< highlight java >}}
kubectl get rs -l app=webapp -o yaml
kubectl get po -l app=webapp -o yaml
{{< / highlight >}}
**66. Delete the deployment you just created and watch all the pods are also being deleted**
{{< highlight java >}}
kubectl delete deploy webapp
kubectl get po -l app=webapp -w
{{< / highlight >}}
**67. Create a deployment of webapp with image nginx:1.17.1 with container port 80 and verify the image version**
{{< highlight java >}}
kubectl create deploy webapp --image=nginx:1.17.1 --dry-run -o yaml > webapp.yaml
// add the port section and create the deployment
kubectl create -f webapp.yaml
// verify
kubectl describe deploy webapp | grep Image

//webapp.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: webapp
  name: webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: webapp
    spec:
      containers:
        - image: nginx:1.17.1
          name: nginx
          ports:
            - containerPort: 80
          resources: {}
status: {}
```

{{< / highlight >}}

**68. Update the deployment with the image version 1.17.4 and verify**
{{< highlight java >}}
kubectl set image deploy/webapp nginx=nginx:1.17.4
kubectl describe deploy webapp | grep Image
{{< / highlight >}}
**69. Check the rollout history and make sure everything is ok after the update**
{{< highlight java >}}
kubectl rollout history deploy webapp
kubectl get deploy webapp --show-labels
kubectl get rs -l app=webapp
kubectl get po -l app=webapp
{{< / highlight >}}
**70. Undo the deployment to the previous version 1.17.1 and verify Image has the previous version**
{{< highlight java >}}
kubectl rollout undo deploy webapp
kubectl describe deploy webapp | grep Image
{{< / highlight >}}
**71. Update the deployment with the image version 1.16.1 and verify the image and also check the rollout history**
{{< highlight java >}}
kubectl set image deploy/webapp nginx=nginx:1.16.1
kubectl describe deploy webapp | grep Image
kubectl rollout history deploy webapp
{{< / highlight >}}
**72. Update the deployment to the Image 1.17.1 and verify everything is ok**
{{< highlight java >}}
kubectl rollout undo deploy webapp --to-revision=3
kubectl describe deploy webapp | grep Image
kubectl rollout status deploy webapp
{{< / highlight >}}
**73. Update the deployment with the wrong image version 1.100 and verify something is wrong with the deployment**
{{< highlight java >}}
kubectl set image deploy/webapp nginx=nginx:1.100
kubectl rollout status deploy webapp (still pending state)
kubectl get pods (ImagePullErr)
{{< / highlight >}}
**74. Undo the deployment with the previous version and verify everything is Ok**
{{< highlight java >}}
kubectl rollout undo deploy webapp
kubectl rollout status deploy webapp
kubectl get pods
{{< / highlight >}}
**75. Check the history of the specific revision of that deployment**
{{< highlight java >}}
kubectl rollout history deploy webapp --revision=7
{{< / highlight >}}
**76. Pause the rollout of the deployment**
{{< highlight java >}}
kubectl rollout pause deploy webapp
{{< / highlight >}}
**77. Update the deployment with the image version latest and check the history and verify nothing is going on**
{{< highlight java >}}
kubectl set image deploy/webapp nginx=nginx:latest
kubectl rollout history deploy webapp (No new revision)
{{< / highlight >}}
**78. Resume the rollout of the deployment**
{{< highlight java >}}
kubectl rollout resume deploy webapp
{{< / highlight >}}
**79. Check the rollout history and verify it has the new version**
{{< highlight java >}}
kubectl rollout history deploy webapp
kubectl rollout history deploy webapp --revision=9
{{< / highlight >}}
**80. Apply the autoscaling to this deployment with minimum 10 and maximum 20 replicas and target CPU of 85% and verify hpa is created and replicas are increased to 10 from 1**
{{< highlight java >}}
kubectl autoscale deploy webapp --min=10 --max=20 --cpu-percent=85
kubectl get hpa
kubectl get pod -l app=webapp
{{< / highlight >}}

**81. Clean the cluster by deleting deployment and hpa you just created**
{{< highlight java >}}
kubectl delete deploy webapp
kubectl delete hpa webapp
{{< / highlight >}}

**82. Create a Job with an image node which prints node version and also verifies there is a pod created for this job**
{{< highlight java >}}
kubectl create job nodeversion --image=node -- node -v
kubectl get job -w
kubectl get pod
{{< / highlight >}}

**83. Get the logs of the job just created**
{{< highlight java >}}
kubectl logs <pod name> // created from the job
84.Output the yaml file for the Job with the image busybox which echos “Hello I am from job”
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job"
{{< / highlight >}}

**84. Copy the above YAML file to hello-job.yaml file and create the job**
{{< highlight java >}}
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
kubectl create -f hello-job.yaml
{{< / highlight >}}

**85. Verify the job and the associated pod is created and check the logs as well**
{{< highlight java >}}
kubectl get job
kubectl get po
kubectl logs hello-job-\*
{{< / highlight >}}

**86. Delete the job we just created**
{{< highlight java >}}
kubectl delete job hello-job
{{< / highlight >}}

**87. Create the same job and make it run 10 times one after one**
{{< highlight java >}}
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
// edit the yaml file to add completions: 10
kubectl create -f hello-job.yaml

//hello-job.yaml

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: hello-job
spec:
  completions: 10
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
        - command:
            - echo
            - Hello I am from job
          image: busybox
          name: hello-job
          resources: {}
      restartPolicy: Never
status: {}
```

{{< / highlight >}}

**88.Output the yaml file for the Job with the image busybox which echos “Hello I am from job”**
{{< highlight java >}}
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job"
{{< / highlight >}}

**89. Watch the job that runs 10 times one by one and verify 10 pods are created and delete those after it’s completed**
{{< highlight java >}}
kubectl get job -w
kubectl get po
kubectl delete job hello-job
{{< / highlight >}}

**90. Create the same job and make it run 10 times parallel**
{{< highlight yaml >}}
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
// edit the yaml file to add parallelism: 10
kubectl create -f hello-job.yaml

//hello-job.yaml

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: hello-job
spec:
  parallelism: 10
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
        - command:
            - echo
            - Hello I am from job
          image: busybox
          name: hello-job
          resources: {}
      restartPolicy: Never
status: {}
```

{{< / highlight >}}

**91. Watch the job that runs 10 times parallelly and verify 10 pods are created and delete those after it’s completed**
{{< highlight java >}}
kubectl get job -w
kubectl get po
kubectl delete job hello-job
{{< / highlight >}}

**92. Create a Cronjob with busybox image that prints date and hello from kubernetes cluster message for every minute**
{{< highlight java >}}
kubectl create cronjob date-job --image=busybox --schedule="_/1 _ \* \* \*" -- bin/sh -c "date; echo Hello from kubernetes cluster"
{{< / highlight >}}

**93. Output the YAML file of the above cronjob**
{{< highlight java >}}
kubectl get cj date-job -o yaml
{{< / highlight >}}

**94. Verify that CronJob creating a separate job and pods for every minute to run and verify the logs of the pod**
{{< highlight java >}}
kubectl get job
kubectl get po
kubectl logs date-job-<jobid>-<pod>
{{< / highlight >}}

**95. Delete the CronJob and verify all the associated jobs and pods are also deleted.**
{{< highlight java >}}
kubectl delete cj date-job
// verify pods and jobs
kubectl get po
kubectl get job
{{< / highlight >}}

### State Persistence (8%)

Practice questions based on these concepts

- Understand PersistentVolumeClaims for Storage

**96. List Persistent Volumes in the cluster**
{{< highlight yaml >}}
kubectl get pv
{{< / highlight >}}
**97. Create a hostPath PersistentVolume named task-pv-volume with storage 10Gi, access modes ReadWriteOnce, storageClassName manual, and volume at /mnt/data and verify**
{{< highlight yaml >}}
kubectl create -f task-pv-volume.yaml
kubectl get pv

//task-pv-volume.yaml

````yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
    ```
{{< / highlight >}}

**98. Create a PersistentVolumeClaim of at least 3Gi storage and access mode ReadWriteOnce and verify status is Bound**
{{< highlight yaml >}}
kubectl create -f task-pv-claim.yaml
kubectl get pvc

//task-pv-claim.yaml
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
      ```
{{< / highlight >}}

**99. Delete persistent volume and PersistentVolumeClaim we just created**
{{< highlight yaml >}}
kubectl delete pvc task-pv-claim
kubectl delete pv task-pv-volume
{{< / highlight >}}
**100. Create a Pod with an image Redis and configure a volume that lasts for the lifetime of the Pod**
{{< highlight yaml >}}
// emptyDir is the volume that lasts for the life of the pod
kubectl create -f redis-storage.yaml

//redis-storage.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage
      mountPath: /data/redis
  volumes:
  - name: redis-storage
    emptyDir: {}
    ```
{{< / highlight >}}

**101. Exec into the above pod and create a file named file.txt with the text ‘This is called the file’ in the path /data/redis and open another tab and exec again with the same pod and verifies file exist in the same path.**
{{< highlight yaml >}}
// first terminal
kubectl exec -it redis-storage /bin/sh
cd /data/redis
echo 'This is called the file' > file.txt
//open another tab
kubectl exec -it redis-storage /bin/sh
cat /data/redis/file.txt
{{< / highlight >}}
**102. Delete the above pod and create again from the same yaml file and verifies there is no file.txt in the path /data/redis**
{{< highlight yaml >}}
kubectl delete pod redis
kubectl create -f redis-storage.yaml
kubectl exec -it redis-storage /bin/sh
cat /data/redis/file.txt // file doesn't exist
{{< / highlight >}}
**103. Create PersistentVolume named task-pv-volume with storage 10Gi, access modes ReadWriteOnce, storageClassName manual, and volume at /mnt/data and Create a PersistentVolumeClaim of at least 3Gi storage and access mode ReadWriteOnce and verify status is Bound**
{{< highlight yaml >}}
kubectl create -f task-pv-volume.yaml
kubectl create -f task-pv-claim.yaml
kubectl get pv
kubectl get pvc
{{< / highlight >}}
**104. Create an nginx pod with containerPort 80 and with a PersistentVolumeClaim task-pv-claim and has a mouth path "/usr/share/nginx/html"**
{{< highlight yaml >}}
kubectl create -f task-pv-pod.yaml

//task-pv-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
          ```
{{< / highlight >}}


### Configuration (18%)

Practice questions based on these concepts

- Understand ConfigMaps
- Understand SecurityContexts
- Define an application’s resource requirements
- Create & Consume Secrets
- Understand ServiceAccounts

**105. List all the configmaps in the cluster**
{{< highlight yaml >}}
kubectl get cm
or
kubectl get configmap
{{< / highlight >}}

**106. Create a configmap called myconfigmap with literal value appname=myapp**
{{< highlight yaml >}}
kubectl create cm myconfigmap --from-literal=appname=myapp
{{< / highlight >}}

**107. Verify the configmap we just created has this data**
{{< highlight yaml >}}
// you will see under data
kubectl get cm -o yaml
or
kubectl describe cm
{{< / highlight >}}

**108. delete the configmap myconfigmap we just created**
{{< highlight yaml >}}
kubectl delete cm myconfigmap
{{< / highlight >}}

**109. Create a file called config.txt with two values key1=value1 and key2=value2 and verify the file**
{{< highlight yaml >}}
cat >> config.txt << EOF
key1=value1
key2=value2
EOF
cat config.txt
{{< / highlight >}}

**110. Create a configmap named keyvalcfgmap and read data from the file config.txt and verify that configmap is created correctly**
{{< highlight yaml >}}
kubectl create cm keyvalcfgmap --from-file=config.txt
kubectl get cm keyvalcfgmap -o yaml
{{< / highlight >}}

**111. Create an nginx pod and load environment values from the above configmap keyvalcfgmap and exec into the pod and verify the environment variables and delete the pod**
{{< highlight yaml >}}
// first run this command to save the pod yml
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-pod.yml
// edit the yml to below file and create
kubectl create -f nginx-pod.yml
// verify
kubectl exec -it nginx -- env
kubectl delete po nginx

//nginx-pod.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      resources: {}
      envFrom:
        - configMapRef:
            name: keyvalcfgmap
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**112. Create an env file file.env with var1=val1 and create a configmap envcfgmap from this env file and verify the configmap**
{{< highlight yaml >}}
echo var1=val1 > file.env
cat file.env
kubectl create cm envcfgmap --from-env-file=file.env
kubectl get cm envcfgmap -o yaml --export
{{< / highlight >}}

**113. Create an nginx pod and load environment values from the above configmap envcfgmap and exec into the pod and verify the environment variables and delete the pod**
{{< highlight yaml >}}
// first run this command to save the pod yml
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-pod.yml
// edit the yml to below file and create
kubectl create -f nginx-pod.yml
// verify
kubectl exec -it nginx -- env
kubectl delete po nginx

//nginx-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      resources: {}
      env:
        - name: ENVIRONMENT
          valueFrom:
            configMapKeyRef:
              name: envcfgmap
              key: var1
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**114. Create a configmap called cfgvolume with values var1=val1, var2=val2 and create an nginx pod with volume nginx-volume which reads data from this configmap cfgvolume and put it on the path /etc/cfg**
{{< highlight yaml >}}
// first create a configmap cfgvolume
kubectl create cm cfgvolume --from-literal=var1=val1 --from-literal=var2=val2
// verify the configmap
kubectl describe cm cfgvolume
// create the config map
kubectl create -f nginx-volume.yml
// exec into the pod
kubectl exec -it nginx -- /bin/sh
// check the path
cd /etc/cfg
ls

//nginx-volume.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  volumes:
    - name: nginx-volume
      configMap:
        name: cfgvolume
  containers:
    - image: nginx
      name: nginx
      resources: {}
      volumeMounts:
        - name: nginx-volume
          mountPath: /etc/cfg
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**115. Create a pod called secbusybox with the image busybox which executes command sleep 3600 and makes sure any Containers in the Pod, all processes run with user ID 1000 and with group id 2000 and verify.**
{{< highlight yaml >}}
// create yml file with dry-run
kubectl run secbusybox --image=busybox --restart=Never --dry-run -o yaml -- /bin/sh -c "sleep 3600;" > busybox.yml
// edit the pod like below and create
kubectl create -f busybox.yml
// verify
kubectl exec -it secbusybox -- sh
id // it will show the id and group

//busybox.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: secbusybox
  name: secbusybox
spec:
  securityContext: # add security context
    runAsUser: 1000
    runAsGroup: 2000
  containers:
    - args:
        - /bin/sh
        - -c
        - sleep 3600;
      image: busybox
      name: secbusybox
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**116. Create the same pod as above this time set the securityContext for the container as well and verify that the securityContext of container overrides the Pod level securityContext.**
{{< highlight yaml >}}
// create yml file with dry-run
kubectl run secbusybox --image=busybox --restart=Never --dry-run -o yaml -- /bin/sh -c "sleep 3600;" > busybox.yml
// edit the pod like below and create
kubectl create -f busybox.yml
// verify
kubectl exec -it secbusybox -- sh
id // you can see container securityContext overides the Pod level

//busybox.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: secbusybox
  name: secbusybox
spec:
  securityContext:
    runAsUser: 1000
  containers:
    - args:
        - /bin/sh
        - -c
        - sleep 3600;
      image: busybox
      securityContext:
        runAsUser: 2000
      name: secbusybox
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**117. Create pod with an nginx image and configure the pod with capabilities NET_ADMIN and SYS_TIME verify the capabilities**
{{< highlight yaml >}}
// create the yaml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// edit as below and create pod
kubectl create -f nginx.yml
// exec and verify
kubectl exec -it nginx -- sh
cd /proc/1
cat status
// you should see these values
CapPrm: 00000000aa0435fb
CapEff: 00000000aa0435fb

//nginx.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      securityContext:
        capabilities:
          add: ["SYS_TIME", "NET_ADMIN"]
      name: nginx
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**118. Create a Pod nginx and specify a memory request and a memory limit of 100Mi and 200Mi respectively.**
{{< highlight yaml >}}
// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl top pod

//nginx.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      resources:
        requests:
          memory: "100Mi"
        limits:
          memory: "200Mi"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**119. Create a Pod nginx and specify a CPU request and a CPU limit of 0.5 and 1 respectively.**
{{< highlight yaml >}}
// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl top pod

//nginx.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      resources:
        requests:
          cpu: "0.5"
        limits:
          cpu: "1"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**120. Create a Pod nginx and specify both CPU, memory requests and limits together and verify.**
{{< highlight yaml >}}
// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl top pod

//nginx.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      resources:
        requests:
          memory: "100Mi"
          cpu: "0.5"
        limits:
          memory: "200Mi"
          cpu: "1"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**121. Create a Pod nginx and specify a memory request and a memory limit of 100Gi and 200Gi respectively which is too big for the nodes and verify pod fails to start because of insufficient memory**
{{< highlight yaml >}}
// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl describe po nginx // you can see pending state

//nginx.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      resources:
        requests:
          memory: "100Gi"
          cpu: "0.5"
        limits:
          memory: "200Gi"
          cpu: "1"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**122. Create a secret mysecret with values user=myuser and password=mypassword**
{{< highlight yaml >}}
kubectl create secret generic my-secret --from-literal=username=user --from-literal=password=mypassword
{{< / highlight >}}

**123. List the secrets in all namespaces**
{{< highlight yaml >}}
kubectl get secret --all-namespaces
{{< / highlight >}}

**124. Output the yaml of the secret created above**
{{< highlight yaml >}}
kubectl get secret my-secret -o yaml
{{< / highlight >}}

**125. Create an nginx pod which reads username as the environment variable**
{{< highlight yaml >}}
// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add env section below and create
kubectl create -f nginx.yml
//verify
kubectl exec -it nginx -- env

//nginx.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      env:
        - name: USER_NAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**126. Create an nginx pod which loads the secret as environment variables**
{{< highlight yaml >}}
// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add env section below and create
kubectl create -f nginx.yml
//verify
kubectl exec -it nginx -- env

//nginx.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      envFrom:
        - secretRef:
            name: my-secret
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

**127. List all the service accounts in the default namespace**
{{< highlight yaml >}}
kubectl get sa
{{< / highlight >}}

**128. List all the service accounts in all namespaces**
{{< highlight yaml >}}
kubectl get sa --all-namespaces
{{< / highlight >}}

**129. Create a service account called admin**
{{< highlight yaml >}}
kubectl create sa admin
{{< / highlight >}}

**130. Output the YAML file for the service account we just created**
{{< highlight yaml >}}
kubectl get sa admin -o yaml
{{< / highlight >}}

**131. Create a busybox pod which executes this command sleep 3600 with the service account admin and verify**
{{< highlight yaml >}}
kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml -- /bin/sh -c "sleep 3600" > busybox.yml
kubectl create -f busybox.yml
// verify
kubectl describe po busybox

//busybox.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  serviceAccountName: admin
  containers:
    - args:
        - /bin/sh
        - -c
        - sleep 3600
      image: busybox
      name: busybox
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

{{< / highlight >}}

### Observability (18%)
Practice questions based on these concepts
* Understand LivenessProbes and ReadinessProbes
* Understand Container Logging
* Understand how to monitor applications in kubernetes
* Understand Debugging in Kubernetes

**132. Create an nginx pod with containerPort 80 and it should only receive traffic only it checks the endpoint / on port 80 and verify and delete the pod.**
{{< highlight yaml >}}
kubectl run nginx --image=nginx --restart=Never --port=80 --dry-run -o yaml > nginx-pod.yaml
// add the readinessProbe section and create
kubectl create -f nginx-pod.yaml
// verify
kubectl describe pod nginx | grep -i readiness
kubectl delete po nginx

//nginx-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    readinessProbe:
      httpGet:
        path: /
        port: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
{{< / highlight >}}

**133. Create an nginx pod with containerPort 80 and it should check the pod running at endpoint / healthz on port 80 and verify and delete the pod.**
{{< highlight yaml >}}
kubectl run nginx --image=nginx --restart=Never --port=80 --dry-run -o yaml > nginx-pod.yaml
// add the livenessProbe section and create
kubectl create -f nginx-pod.yaml
// verify
kubectl describe pod nginx | grep -i readiness
kubectl delete po nginx

//nginx-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    livenessProbe:
      httpGet:
        path: /healthz
        port: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
{{< / highlight >}}

**134. Create an nginx pod with containerPort 80 and it should check the pod running at endpoint /healthz on port 80 and it should only receive traffic only it checks the endpoint / on port 80. verify the pod.**
{{< highlight yaml >}}
kubectl run nginx --image=nginx --restart=Never --port=80 --dry-run -o yaml > nginx-pod.yaml
// add the livenessProbe and readiness section and create
kubectl create -f nginx-pod.yaml
// verify
kubectl describe pod nginx | grep -i readiness
kubectl describe pod nginx | grep -i liveness

//nginx-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    livenessProbe:
      httpGet:
        path: /healthz
        port: 80
    readinessProbe:
      httpGet:
        path: /
        port: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
{{< / highlight >}}

**135. Check what all are the options that we can configure with readiness and liveness probes**
{{< highlight yaml >}}
kubectl explain Pod.spec.containers.livenessProbe
kubectl explain Pod.spec.containers.readinessProbe
{{< / highlight >}}

**136. Create the pod nginx with the above liveness and readiness probes so that it should wait for 20 seconds before it checks liveness and readiness probes and it should check every 25 seconds.**
{{< highlight yaml >}}
kubectl create -f nginx-pod.yaml

//nginx-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    livenessProbe:
      initialDelaySeconds: 20
      periodSeconds: 25
      httpGet:
        path: /healthz
        port: 80
    readinessProbe:
      initialDelaySeconds: 20
      periodSeconds: 25
      httpGet:
        path: /
        port: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
{{< / highlight >}}

**137. Create a busybox pod with this command “echo I am from busybox pod; sleep 3600;” and verify the logs.**
{{< highlight yaml >}}
kubectl run busybox --image=busybox --restart=Never -- /bin/sh -c "echo I am from busybox pod; sleep 3600;"
kubectl logs busybox
{{< / highlight >}}

**138. copy the logs of the above pod to the busybox-logs.txt and verify**
{{< highlight yaml >}}
kubectl logs busybox > busybox-logs.txt
cat busybox-logs.txt
{{< / highlight >}}

**139. List all the events sorted by timestamp and put them into file.log and verify**
{{< highlight yaml >}}
kubectl get events --sort-by=.metadata.creationTimestamp
// putting them into file.log
kubectl get events --sort-by=.metadata.creationTimestamp > file.log
cat file.log
{{< / highlight >}}

**140. Create a pod with an image alpine which executes this command ”while true; do echo ‘Hi I am from alpine’; sleep 5; done” and verify and follow the logs of the pod.**
{{< highlight yaml >}}
// create the pod
kubectl run hello --image=alpine --restart=Never  -- /bin/sh -c "while true; do echo 'Hi I am from Alpine'; sleep 5;done"
// verify and follow the logs
kubectl logs --follow hello
{{< / highlight >}}

**141. Create the pod with this kubectl create -f https://gist.githubusercontent.com/bbachi/212168375b39e36e2e2984c097167b00/raw/1fd63509c3ae3a3d3da844640fb4cca744543c1c/not-running.yml. The pod is not in the running state. Debug it.**
{{< highlight yaml >}}
// create the pod
kubectl create -f https://gist.githubusercontent.com/bbachi/212168375b39e36e2e2984c097167b00/raw/1fd63509c3ae3a3d3da844640fb4cca744543c1c/not-running.yml
// get the pod
kubectl get pod not-running
kubectl describe po not-running
// it clearly says ImagePullBackOff something wrong with image
kubectl edit pod not-running // it will open vim editor
                     or
kubectl set image pod/not-running not-running=nginx
{{< / highlight >}}

**142. This following yaml creates 4 namespaces and 4 pods. One of the pod in one of the namespaces are not in the running state. Debug and fix it. https://gist.githubusercontent.com/bbachi/1f001f10337234d46806929d12245397/raw/84b7295fb077f15de979fec5b3f7a13fc69c6d83/problem-pod.yaml.
kubectl create -f https://gist.githubusercontent.com/bbachi/1f001f10337234d46806929d12245397/raw/84b7295fb077f15de979fec5b3f7a13fc69c6d83/problem-pod.yaml**
{{< highlight yaml >}}
// get all the pods in all namespaces
kubectl get po --all-namespaces
// find out which pod is not running
kubectl get po -n namespace2
// update the image
kubectl set image pod/pod2 pod2=nginx -n namespace2
// verify again
kubectl get po -n namespace2
{{< / highlight >}}

**143. Get the memory and CPU usage of all the pods and find out top 3 pods which have the highest usage and put them into the cpu-usage.txt file**
{{< highlight yaml >}}
// get the top 3 hungry pods
kubectl top pod --all-namespaces | sort --reverse --key 3 --numeric | head -3
// putting into file
kubectl top pod --all-namespaces | sort --reverse --key 3 --numeric | head -3 > cpu-usage.txt
// verify
cat cpu-usage.txt
{{< / highlight >}}

### Services and Networking (13%)
Practice questions based on these concepts
* Understand Services
* Demonstrate a basic understanding of NetworkPolicies

**144. Create an nginx pod with a yaml file with label my-nginx and expose the port 80**
{{< highlight yaml >}}
kubectl run nginx --image=nginx --restart=Never --port=80 --dry-run -o yaml > nginx.yaml
// edit the label app: my-nginx and create the pod
kubectl create -f nginx.yaml

//nginx.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    app: my-nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
{{< / highlight >}}

**145. Create the service for this nginx pod with the pod selector app: my-nginx**
{{< highlight yaml >}}
// create the below service
kubectl create -f nginx-svc.yaml

//nginx-svc.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
      ```
{{< / highlight >}}

**146. Find out the label of the pod and verify the service has the same label**
{{< highlight yaml >}}
// get the pod with labels
kubectl get po nginx --show-labels
// get the service and chekc the selector column
kubectl get svc my-service -o wide
{{< / highlight >}}

**147. Delete the service and create the service with kubectl expose command and verify the label**
{{< highlight yaml >}}
// delete the service
kubectl delete svc my-service
// create the service again
kubectl expose po nginx --port=80 --target-port=9376
// verify the label
kubectl get svc -l app=my-nginx
{{< / highlight >}}

**148. Delete the service and create the service again with type NodePort**
{{< highlight yaml >}}
// delete the service
kubectl delete svc nginx
// create service with expose command
kubectl expose po nginx --port=80 --type=NodePort
{{< / highlight >}}

**149. Create the temporary busybox pod and hit the service. Verify the service that it should return the nginx page index.html.**
{{< highlight yaml >}}
// get the clusterIP from this command
kubectl get svc nginx -o wide
// create temporary busybox to check the nodeport
kubectl run busybox --image=busybox --restart=Never -it --rm -- wget -o- <Cluster IP>:80
{{< / highlight >}}

**150. Create a NetworkPolicy which denies all ingress traffic**
{{< highlight yaml >}}
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ```
 {{< / highlight >}}

### Conclusion
CKAD is a performance-based exam and it’s all about completing 19 questions within 2 hours. We need a lot of practice for it. These 150 questions give you enough practice for the exam. The more you practice the more comfortable you feel during the exam. Practice. Practice. Practice.
````
