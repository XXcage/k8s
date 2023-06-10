# k8s
# Part 1

K8s exam:
1. Deploy a pod named nginx-pod using the nginx:alpine image.
Name: nginx-pod-yourname
Image: nginx:alpine
>kubectl run nginx-pod-rz --image=nginx:alpine --port=80
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-pod-rz
    spec:
      containers:
      - name: nginx-pod-rz
        image: nginx:alpine
        ports:
        - containerPort: 80
---
2. Deploy a messaging pod using the redis:alpine image with the labels set to tier=msg.
Pod Name: messaging
Image: redis:alpine
Labels: tier=msg
>kubectl run messaging --image=redis:alpine --labels=tier=msg
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: messaging
      labels:
        tier: msg
    spec:
      containers:
        - name: redis-container
          image: redis:alpine
---
3. Create a namespace named apx-x998-yourname
>kubectl create namespace apx-x998-rz
---
    apiVersion: v1
    kind: Namespace
    metadata:
      name: apx-x998-rz
---
4. Get the list of nodes in JSON format and store it in a file at /tmp/nodes-yourname
>kubectl get nodes -o json >> /tmp/nodes-rz.json
5. Create a service messaging-service to expose the messaging application within the
cluster on port 6379.
a. Use imperative commands - kubectl
b. Service: messaging-service
c. Port: 6379
d. Type: ClusterIp
e. Use the right labels
---
    kubectl create service clusterip messaging-service --tcp=6379:6379 --labels="tier=msg"
---
6. Create a service messaging-service to expose the messaging application within the
cluster on port 6379.
a. Service: messaging-service
b. Port: 6379
c. Type: ClusterIp
d. Use the right labels
---
    apiVersion: v1
    kind: Service
    metadata:
      name: messaging-service
      labels:
        tier: msg
    spec:
      selector:
        tier: msg
      ports:
        - protocol: TCP
          port: 6379
          targetPort: 6379
      type: ClusterIP
---
7. Create a deployment named hr-web-app using the image kodekloud/webapp-color with
2 replicas
a. Name: hr-web-app
b. Image: kodekloud/webapp-color
c. Replicas: 2
---
>kubectl create deployment hr-web-app --image=kodekloud/webapp-color --replicas=2
---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: hr-web-app
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: hr-web-app
      template:
        metadata:
          labels:
            app: hr-web-app
        spec:
          containers:
            - name: hr-web-app
              image: kodekloud/webapp-color
---
8. Create a static pod named static-busybox on the master node that uses the busybox
image and the command sleep 1000
a. Name: static-busybox
b. Image: busybox
>sudo nano /etc/kubernetes/manifests/static-busybox.yaml
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: static-busybox
    spec:
      containers:
        - name: busybox-container
          image: busybox
          command:
            - sleep
            - "1000"
---
9. Create a POD in the finance-yourname namespace named temp-bus with the image
redis:alpine
a. Name: temp-bus
b. Image Name: redis:alpine
>kubectl run temp-bus --image=redis:alpine --namespace=finance-yourname
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: temp-bus
      namespace: finance-rz
    spec:
      containers:
        - name: temp-bus
          image: redis:alpine
---
10. Create a Persistent Volume with the given specification
a. Volume Name: pv-analytics
b. Storage: 100Mi
c. Access modes: ReadWriteMany
d. Host Path: /pv/data-analytics
>kubectl create pv pv-analytics --capacity=100Mi --access-modes=ReadWriteMany --host-path=/pv/data-analytics
---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-analytics
    spec:
      capacity:
        storage: 100Mi
      accessModes:
        - ReadWriteMany
      hostPath:
        path: /pv/data-analytics
---
11. Create a Pod called redis-storage-yourname with image: redis:alpine with a Volume of
type emptyDir that lasts for the life of the Pod. specs:.
a. Pod named 'redis-storage-yourname'
b. Pod 'redis-storage-yourname' uses Volume type of emptyDir
c. Pod 'redis-storage-yourname' uses volumeMount with mountPath = /data/redis
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: redis-storage-rz
    spec:
      volumes:
        - name: redis-storage
          emptyDir: {}
      containers:
        - name: redis-container
          image: redis:alpine
          volumeMounts:
            - name: redis-storage
              mountPath: /data/redis
---
12. Create this pod and attached it a persistent volume called pv-1
a. Make sure the PV mountPath is hostbase : /data
apiVersion: v1
kind: Pod
metadata:
creationTimestamp: null
labels:
run: use-pv
name: use-pvspec-yourname
containers:
- image: nginx
name: use-pv
resources: {}
dnsPolicy: ClusterFirst
restartPolicy: Always
status: {}
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: use-pvspec-rz
      labels:
        run: use-pv
    spec:
      containers:
        - name: use-pv
          image: nginx
          volumeMounts:
            - name: pv-1
              mountPath: /data
      volumes:
        - name: pv-1
          persistentVolumeClaim:
            claimName: pv-1
---
13. Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica.
Record the version. Next upgrade the deployment to version 1.17 using rolling update.
Make sure that the version upgrade is recorded in the resource annotation.
a. Deployment : nginx-deploy. Image: nginx:1.16
b. Image: nginx:1.16
c. Task: Upgrade the version of the deployment to 1:17
d. Task: Record the changes for the image upgrade
---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deploy
      annotations:
        kubernetes.io/change-cause: "start deployment at 1.16"
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
            - name: nginx
              image: nginx:1.16
              ports:
                - containerPort: 80
---
>kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record=true --record-annotation="kubernetes.io/change-cause=Upgrade to version 1.17"
14. Create an nginx pod called nginx-resolver using image nginx, expose it internally with a
service called nginx-resolver-service. Test that you are able to look up the service and
pod names from within the cluster. Use the image: busybox:1.28 for dns lookup. Record
results in /root/nginx-yourname.svc and /root/nginx-yourname.pod
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-resolver
      labels:
        app: nginx-resolver
    spec:
      containers:
      - name: nginx
        image: nginx
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-resolver-service
    spec:
      selector:
        app: nginx-resolver
      ports:
      - protocol: TCP
        port: 80
        targetPort: 80
---
>kubectl run busybox-pod --image=busybox:1.28 --command -- sleep 3600 && \
kubectl get pod busybox-pod && \
kubectl exec busybox-pod -- nslookup nginx-resolver-service > /home/bckp/proj4/results/nginx-rz.svc && \
kubectl exec busybox-pod -- nslookup nginx-resolver > /home/bckp/proj4/results/nginx-rz.pod && \
---
>Server:    10.96.0.10
>Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local
>Name:      nginx-resolver-service
>Address 1: 10.101.55.131 nginx-resolver-service.default.svc.cluster.local

>Server:    10.96.0.10
>Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local
>nslookup: can't resolve 'nginx-resolver'

15. Create a static pod on node01 called nginx-critical with image nginx. Create this pod on
node01 and make sure that it is recreated/restarted automatically in case of a failure.
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-critical
      namespace: kube-system
    spec:
      restartPolicy: Always
      nodeName: node01
      containers:
        - name: nginx
          image: nginx
---
>sudo mv nginx-critical.yaml /etc/kubernetes/manifests/

16. Create a pod called multi-pod with two containers.
Container 1, name: alpha, image: nginx
Container 2: beta, image: busybox, command sleep 4800.
a. Environment Variables:
i. container 1:
ii. name: alpha
iii. Container 2:
iv. name: beta
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: multi-pod
    spec:
      containers:
      - name: alpha
        image: nginx
        env:
        - name: name
          value: alpha
      - name: beta
        image: busybox
        command: ["sleep", "4800"]
        env:
        - name: name
          value: beta
---
# Part 2
# Pod Design Questions:
● Understand how to use Labels, Selectors and Annotations
● Understand Deployments and how to perform rolling updates
● Understand Deployments and how to perform rollbacks
● Understand Jobs and CronJobs
1. Type the command for:
Get pods with label information
>kubectl get pods --show-labels
2. Create 5 nginx pods in which two of them is labeled env=prod and
three of them is labeled env=dev
---
    kubectl run nginx-prodpod-1 --image=nginx --labels=env=prod
    kubectl run nginx-prodpod-2 --image=nginx --labels=env=prod
    kubectl run nginx-devpod-1 --image=nginx --labels=env=dev
    kubectl run nginx-devpod-2 --image=nginx --labels=env=dev
    kubectl run nginx-devpod-3 --image=nginx --labels=env=dev
---
3. Verify all the pods are created with correct labels
>kubectl get pods --show-labels
4. Get the pods with label env=dev
>kubectl get pods -l env=dev
5. Get the pods with label env=dev and also output the labels
>kubectl get pods -l env=dev --show-labels
6. Get the pods with label env=prod
>kubectl get pods -l env=prod
7. Get the pods with label env=prod and also output the labels
>kubectl get pods -l env=prod --show-labels
8. Get the pods with label env
>kubectl get pods -l env
9. Get the pods with labels env=dev and env=prod
>kubectl get pods -l env=dev,env=prod
10. Get the pods with labels env=dev and env=prod and output the
labels as well
>kubectl get pods -l env=dev,env=prod --show-labels
11. Change the label for one of the pod to env=uat and list all the pods to
verify
>kubectl label pod nginx-devpod-1 env=uat --overwrite
>kubectl get pods --show-labels

12. Remove the labels for the pods that we created now and verify all the
labels are removed
---
    kubectl label pod nginx-prodpod-1 nginx-prodpod-2 nginx-devpod-1 nginx-devpod-2 nginx-devpod-3 env-
    kubectl get pods --show-labels
---
13. Let’s add the label app=nginx for all the pods and verify (using
kubectl)
---
    kubectl label pod nginx-prodpod-1 nginx-prodpod-2 nginx-devpod-1 nginx-devpod-2 nginx-devpod-3 app=nginx
    kubectl get pods --show-labels
---
14.Get all the nodes with labels (if using minikube you would get only
master node)
>kubectl get nodes --show-labels
15. Label the worker node nodeName=nginxnode
>kubectl label node k8s-node1 nodeName=nginxnode
---
16.Create a Pod that will be deployed on the worker node with the label
nodeName=nginxnode
Add the nodeSelector to the below and create the pod
---
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
---
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
      nodeSelector:
        nodeName: nginxnode
    status: {}
---
17. Verify the pod that it is scheduled with the node selector on the right
node... fix it if it’s not behind scheduled.
>kubectl get pods -A -o wide
18. Verify the pod nginx that we just created has this label
>kubectl get pods -l run=nginx
---
# Part 3
# Deployments:

1. Create a deployment called webapp with image nginx with 5 replicas
a. Use the below command to create a yaml file.
i. kubectl create deploy webapp --image=nginx --dry-run -o
yaml > webapp.yaml
ii. Edit it and add 5 replica’s

---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      creationTimestamp: null
      labels:
        app: webapp
      name: webapp
    spec:
      #replicas: 1
      replicas: 6
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
---
2. Get the deployment rollout status
>kubectl rollout status deployment webapp
3. Get the replicaset that created with this deployment
---
    kubectl get replicaset -l app=webapp
    kubectl get replicasets.apps webapp
---
4. EXPORT the yaml of the replicaset and pods of this deployment
---
    kubectl get replicaset -l app=webapp --export=true --output=yaml > webappreplicaset.yaml
    kubectl get pods -l app=webapp --export=true --output=yaml > webapppods.yaml
---
5. Delete the deployment you just created and watch all the pods are
also being deleted
>kubectl delete deployment webapp --cascade=true --watch
6. Create a deployment of webapp with image nginx:1.17.1 with
container port 80 and verify the image version
a. kubectl create deploy webapp --image=nginx:1.17.1 --dry-run -o
yaml > webapp.yaml
b. add the port section (80) and create the deployment
>kubectl create deploy webapp --image=nginx:1.17.1 --port=80 --dry-run -o yaml > webapp.yaml
7. Update the deployment with the image version 1.17.4 and verify
---
kubectl set image deployment/webapp nginx=nginx:1.17.4
kubectl rollout status deployment/webapp
---
8. Check the rollout history and make sure everything is ok after the
update
>kubectl rollout history deployment/webapp
9. Undo the deployment to the previous version 1.17.1 and verify Image
has the previous version
>kubectl rollout undo deployment/webapp
>kubectl describe deployment webapp | grep -i image

10. Update the deployment with the wrong image version 1.100 and
verify something is wrong with the deployment
a. Expect: kubectl get pods (ImagePullErr)
b. Undo the deployment with the previous version and verify
everything is Ok
c. kubectl rollout history deploy webapp --revision=7
d. Check the history of the specific revision of that deployment
e. update the deployment with the image version latest and check
the history and verify nothing is going on
---
    kubectl set image deployment/webapp webapp=nginx:1.100
    kubectl get pods
    kubectl rollout undo deployment/webapp
    kubectl describe deployment webapp | grep -i image
    kubectl rollout history deploy webapp --revision=7
    kubectl set image deployment/webapp webapp=nginx:latest
    kubectl rollout history deploy webapp
---
11. Apply the autoscaling to this deployment with minimum 10 and
maximum 20 replicas and target CPU of 85% and verify hpa is
created and replicas are increased to 10 from 1
---
    apiVersion: autoscaling/v2
    kind: HorizontalPodAutoscalers
    metadata:
      name: webapp-hpa
    spec:
      scaleTargetRef:
        apiVersion: apps/v1
        kind: Deployment
        name: webapp
      minReplicas: 10
      maxReplicas: 20
      metrics:
      - type: Resource
        resource:
          name: cpu
          target:
            type: Utilization
            averageUtilization: 85
---
>kubectl apply -f hpa.yaml   
>error: resource mapping not found for name: "webapp-hpa" namespace: "" from "hpa.yaml": no matches for kind "HorizontalPodAutoscalers" in version "autoscaling/v2"   
>ensure CRDs are installed first   

~~12.~~

13. Clean the cluster by deleting deployment and hpa you just created

>kubectl delete deployments.apps webapp

14. Create a job and make it run 10 times one after one (run > exit > run >exit ..) using the following configuration:
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml”
a. Add to the above job completions: 10 inside the yaml
---
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: hello-job
    spec:
      completions: 10
      template:
        spec:
          containers:
          - name: busybox
            image: busybox
            command: ["echo", "Hello I am from job"]
          restartPolicy: Never
---
# Part 4
# CONFIG MAP:
1. Create a file called config.txt with two values key1=value1 and
---
    key2=value2 and verify the file
    cat >> config.txt << EOF
    key1=value1
    key2=value2
    EOF
    cat config.txt
---
2. Create a configmap named keyvalcfgmap and read data from the file
config.txt and verify that configmap is created correctly
---
    kubectl create configmap keyvalcfgmap --from-file=config.txt
    kubectl get configmap keyvalcfgmap -o yaml
---
    apiVersion: v1
    data:
      config.txt: |
        key1=value1
        key2=value2
    kind: ConfigMap
    metadata:
      creationTimestamp: "2023-06-10T04:15:37Z"
      name: keyvalcfgmap
      namespace: default
      resourceVersion: "478521"
      uid: d0c7f56d-0864-41f2-8421-f189a604c9f7
---
3. Create an nginx pod and load environment values from the above
configmap keyvalcfgmap and exec into the pod and verify the
environment variables and delete the pod
// first run this command to save the pod yml
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml >
nginx-pod.yml
---
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-pod
    spec:
      containers:
        - name: nginx
          image: nginx
          env:
            - name: KEY1
              valueFrom:
                configMapKeyRef:
                  name: keyvalcfgmap
                  key: key1
            - name: KEY2
              valueFrom:
                configMapKeyRef:
                  name: keyvalcfgmap
                  key: key2
---
