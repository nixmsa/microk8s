mahsan@microk8s:~$ sudo microk8s start
[sudo] password for mahsan:
Started.
mahsan@microk8s:~$  microk8s enable storage
Enabling default storage class
[sudo] password for mahsan:
deployment.apps/hostpath-provisioner created
storageclass.storage.k8s.io/microk8s-hostpath created
serviceaccount/microk8s-hostpath created
clusterrole.rbac.authorization.k8s.io/microk8s-hostpath created
clusterrolebinding.rbac.authorization.k8s.io/microk8s-hostpath created
Storage will be available soon
mahsan@microk8s:~$


***

ahsan@microk8s:~$ microk8s kubectl get pods -A
NAMESPACE     NAME                                         READY   STATUS             RESTARTS       AGE
kube-system   kubernetes-dashboard-585bdb5648-2q6gn        1/1     Running            6 (27m ago)    46h
kube-system   dashboard-metrics-scraper-69d9497b54-jn2ht   1/1     Running            11 (27m ago)   46h
kube-system   coredns-64c6478b6c-p7v4t                     1/1     Running            13 (27m ago)   10h
kube-system   calico-kube-controllers-7787cd4f69-shxz9     1/1     Running            6 (27m ago)    46h
kube-system   calico-node-cg4m4                            1/1     Running            4 (27m ago)    46h
kube-system   metrics-server-679c5f986d-h9wwh              0/1     CrashLoopBackOff   37 (38s ago)   46h
kube-system   hostpath-provisioner-7764447d7c-q6fps        1/1     Running            0              42s
mahsan@microk8s:~$


***
mahsan@microk8s:~/microk8s$ microk8s kubectl create -f pvc.yaml
persistentvolumeclaim/my-pvc created
mahsan@microk8s:~/microk8s$ bash
mahsan@microk8s:~/microk8s$ microk8s kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS        REASON                         AGE
pvc-de9a11b3-967b-495c-b54d-6c8354460ac8   1Gi        RWO            Delete           Bound    default/my-pvc   microk8s-hostpath                                  53s
mahsan@microk8s:~/microk8s$ microk8s kubectl get pvc
NAME     STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS        AGE
my-pvc   Bound    pvc-de9a11b3-967b-495c-b54d-6c8354460ac8   1Gi        RWO            microk8s-hostpath   75s
mahsan@microk8s:~/microk8s$ cat pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: microk8s-hostpath
  resources:
    requests:
      storage: 1Gi
mahsan@microk8s:~/microk8s$

***

mahsan@microk8s:~/microk8s$ microk8s kubectl  create -f pvc-pod.yaml
pod/nginx-mypv created
mahsan@microk8s:~/microk8s$ cat pvc-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-mypv
  labels:
    name: nginx-mypv
spec:
  containers:
    - name: nginx-mypv
      image: fedora/nginx
      ports:
        - name: web
          containerPort: 80
      volumeMounts:
        - name: my-persistent-volume
          mountPath: /usr/share/nginx/html
  volumes:
    - name: my-persistent-volume
      persistentVolumeClaim:
        claimName: my-pvc
mahsan@microk8s:~/microk8s$

***
mahsan@microk8s:~/microk8s$ microk8s kubectl get pods -o wide
NAME         READY   STATUS    RESTARTS   AGE   IP             NODE       NOMINATED NODE   READINESS GATES
nginx-mypv   1/1     Running   0          50s   10.1.128.222   microk8s   <none>           <none>

***

mahsan@microk8s:/var/snap/microk8s/common/default-storage/default-my-pvc-pvc-de9a11b3-967b-495c-b54d-6c8354460ac8$ echo "Hello MicroK8s" > index.html
mahsan@microk8s:/var/snap/microk8s/common/default-storage/default-my-pvc-pvc-de9a11b3-967b-495c-b54d-6c8354460ac8$ ls -ltr
total 4
-rw-rw-r-- 1 mahsan mahsan 15 Feb  8 21:50 index.html
mahsan@microk8s:/var/snap/microk8s/common/default-storage/default-my-pvc-pvc-de9a11b3-967b-495c-b54d-6c8354460ac8$ microk8s kubectl get pods -o wide
NAME         READY   STATUS    RESTARTS   AGE    IP             NODE       NOMINATED NODE   READINESS GATES
nginx-mypv   1/1     Running   0          7m3s   10.1.128.222   microk8s   <none>           <none>
mahsan@microk8s:/var/snap/microk8s/common/default-storage/default-my-pvc-pvc-de9a11b3-967b-495c-b54d-6c8354460ac8$ curl 10.1.128.222
Hello MicroK8s

