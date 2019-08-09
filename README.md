[root@node1 ~]# k get nodes
NAME    STATUS   ROLES         AGE   VERSION
node1   Ready    master,node   20d   v1.13.5
node2   Ready    master,node   20d   v1.13.5
node3   Ready    node          20d   v1.13.5
node4   Ready    node          20d   v1.13.5
node5   Ready    node          17d   v1.13.5

[root@node1 ~]# kubectl inspect gpushare -d

NAME:       node5
IPADDRESS:  10.202.115.1

NAME         NAMESPACE  GPU0(Allocated)
Allocated :  0 (0%)
Total :      7
----------


Allocated/Total GPU Memory In Cluster:  0/7 (0%)


[root@node1 ~]# k describe node node5
Name:               node5
Roles:              node
Labels:             accelerator=nvidia-tesla-m6
                    beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    gpushare=true
                    kubernetes.io/hostname=node5
                    node-role.kubernetes.io/node=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VtepMAC":"52:d4:22:67:95:62"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 10.202.115.1
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Tue, 23 Jul 2019 00:16:45 +0200
Taints:             <none>
Unschedulable:      false
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Fri, 09 Aug 2019 09:54:30 +0200   Sat, 27 Jul 2019 19:15:28 +0200   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure     False   Fri, 09 Aug 2019 09:54:30 +0200   Sat, 27 Jul 2019 19:15:28 +0200   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Fri, 09 Aug 2019 09:54:30 +0200   Sat, 27 Jul 2019 19:15:28 +0200   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready            True    Fri, 09 Aug 2019 09:54:30 +0200   Sat, 27 Jul 2019 19:15:28 +0200   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  10.202.115.1
  Hostname:    node5
Capacity:
 aliyun.com/gpu-count:  1
 aliyun.com/gpu-mem:    7
 cpu:                   24
 ephemeral-storage:     66519312Ki
 hugepages-1Gi:         0
 hugepages-2Mi:         0
 memory:                131820584Ki
 nvidia.com/gpu:        0
 pods:                  110
Allocatable:
 aliyun.com/gpu-count:  1
 aliyun.com/gpu-mem:    7
 cpu:                   23900m
 ephemeral-storage:     61304197838
 hugepages-1Gi:         0
 hugepages-2Mi:         0
 memory:                131468184Ki
 nvidia.com/gpu:        0
 pods:                  110
System Info:
 Machine ID:                 af4febb5e079447790317f30213a8921
 System UUID:                FCDB1AE4-95D9-4EEA-98DD-751D914A6475
 Boot ID:                    ba64e9ec-eff5-4511-8da2-e172e458baac
 Kernel Version:             3.10.0-957.21.3.el7.x86_64
 OS Image:                   CentOS Linux 7 (Core)
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://19.3.0
 Kubelet Version:            v1.13.5
 Kube-Proxy Version:         v1.13.5
PodCIDR:                     10.233.68.0/24
Non-terminated Pods:         (6 in total)
  Namespace                  Name                               CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                               ------------  ----------  ---------------  -------------  ---
  ingress-nginx              ingress-nginx-controller-zhk6w     0 (0%)        0 (0%)      0 (0%)           0 (0%)         17d
  kube-system                gpushare-device-plugin-ds-6279r    1 (4%)        1 (4%)      300Mi (0%)       300Mi (0%)     2d12h
  kube-system                kube-flannel-j9679                 150m (0%)     300m (1%)   64M (0%)         500M (0%)      17d
  kube-system                kube-proxy-5h87w                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         17d
  kube-system                nginx-proxy-node5                  25m (0%)      0 (0%)      32M (0%)         0 (0%)         17d
  metallb-system             speaker-tklv7                      100m (0%)     100m (0%)   100Mi (0%)       100Mi (0%)     17d
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource              Requests       Limits
  --------              --------       ------
  cpu                   1275m (5%)     1400m (5%)
  memory                503350Ki (0%)  919430400 (0%)
  ephemeral-storage     0 (0%)         0 (0%)
  aliyun.com/gpu-count  0              0
  aliyun.com/gpu-mem    0              0
  nvidia.com/gpu        0              0
Events:                 <none>



[root@node1 gpu-test]# kubectl create -f tf-jupyter-share.yml
deployment.extensions/tf-jupyter created
service/tf-jupyter-service created

[root@node1 gpu-test]# kubectl create -f tf-jupyter-share2.yml
deployment.extensions/tf-jupyter2 created
service/tf-jupyter-service2 created




[root@node1 gpu-test]#  kubectl inspect gpushare -d

NAME:       node5
IPADDRESS:  10.202.115.1

NAME                          NAMESPACE  GPU0(Allocated)
tf-jupyter-954ffbbf8-lztc9    default    3
tf-jupyter2-69b6f97566-j4f4w  default    3
Allocated :                   6 (85%)
Total :                       7
----------------------------------------------------------------------------------------


Allocated/Total GPU Memory In Cluster:  6/7 (85%)

####################################
# Get jupyter notebook token

[root@node1 gpu-test]# k get pod
NAME                           READY   STATUS    RESTARTS   AGE
minio-55679d65cc-4hgm4         1/1     Running   2          20d
tf-jupyter-954ffbbf8-lztc9     1/1     Running   0          72s
tf-jupyter2-69b6f97566-j4f4w   1/1     Running   0          55s


[root@node1 gpu-test]# k get svc
NAME                  TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
kubernetes            ClusterIP      10.233.0.1      <none>           443/TCP          20d
minio-service         LoadBalancer   10.233.39.183   10.202.115.190   9000:31468/TCP   20d
tf-jupyter-service    LoadBalancer   10.233.58.223   10.202.115.191   8888:30061/TCP   4m52s
tf-jupyter-service2   LoadBalancer   10.233.8.218    10.202.115.192   8888:30062/TCP   4m35s



[root@node1 gpu-test]# k exec -ti tf-jupyter-954ffbbf8-lztc9 -- jupyter notebook list
Currently running servers:
http://0.0.0.0:8888/?token=d90df992165c679047c52009d47f9b0cb027d84dbcf1ceb2 :: /notebooks

[root@node1 gpu-test]# k exec -ti  tf-jupyter2-69b6f97566-j4f4w -- jupyter notebook list
Currently running servers:
http://0.0.0.0:8888/?token=31a0dc813da97c4ae2ce3d1b9967b7e0fce1a86d4ee639dd :: /notebooks

# Open both notebook
http://10.202.115.191:8888/?token=d90df992165c679047c52009d47f9b0cb027d84dbcf1ceb2

http://10.202.115.192:8888/?token=31a0dc813da97c4ae2ce3d1b9967b7e0fce1a86d4ee639dd


#  Edit both notebook ( 3_mnist_from_scratch.ipynb ) 

###########################    Edit  Jupyter Notebooks

Entry[15] :
# Give a fraction of GPU memory to Tensorflow
fraction = round( 3 * 0.7 / 7 , 1 )
config = tf.ConfigProto()
config.gpu_options.per_process_gpu_memory_fraction = fraction

# Create a new interactive session that we'll use in
# subsequent code cells.
s = tf.InteractiveSession(config=config)


###########################    Run  Jupyter Notebooks

#  Prepare in both notebook  menus  "Kernel / Retart & Run All"

# Clic both run button

