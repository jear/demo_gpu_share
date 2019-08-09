## About

The Nvidia GPU sharing device plugin for Kubernetes is a Daemonset that allows you to automatically:
- Expose the GPU Memory and GPU count on the node of your cluster
- Run GPU sharing enabled containers in your Kubernetes cluster.

For more info, please refer [gpusharing scheduler extender](https://github.com/AliyunContainerService/gpushare-scheduler-extender)


------------------------------------------

### k8s cluster node5 has 1 GPU0 with 8GiB of RAM 
------------------------------------------

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


### Let's create 2 tf-jupyter deployments with each  3GiB of GPU0 RAM 
----------------------------------------------------------

    [root@node1 gpu-test]# kubectl create -f tf-jupyter-share.yml
    deployment.extensions/tf-jupyter created
    service/tf-jupyter-service created

    [root@node1 gpu-test]# kubectl create -f tf-jupyter-share2.yml
    deployment.extensions/tf-jupyter2 created
    service/tf-jupyter-service2 created

### Let's check GPU0 RAM fraction allocation for each pod 
----------------------------------------------------------

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

###
### Get jupyter notebook IP:PORT and  token

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

###  Open both notebook
    http://10.202.115.191:8888/?token=d90df992165c679047c52009d47f9b0cb027d84dbcf1ceb2

    http://10.202.115.192:8888/?token=31a0dc813da97c4ae2ce3d1b9967b7e0fce1a86d4ee639dd


###   Edit both notebooks ( 3_mnist_from_scratch.ipynb ) 


    Entry[15] :
    # Give a fraction of GPU memory to Tensorflow
    fraction = round( 3 * 0.7 / 7 , 1 )
    config = tf.ConfigProto()
    config.gpu_options.per_process_gpu_memory_fraction = fraction

    # Create a new interactive session that we'll use in
    # subsequent code cells.
    s = tf.InteractiveSession(config=config)


###     Run  Jupyter Notebooks

###  Prepare in both notebook  menus  "Kernel / Retart & Run All"

### Clic both run button

