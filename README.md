# AWS_EKS_Project_Example

eksctl create cluster --name lw-cluster --region ap-south-1 --version 1.31 --nodegroup-name lw-ng --instance-types t2.xlarge --with-oidc --nodes 2 --nodes-min 1 --nodes-max 5 --node-volume-size 40 --node-volume-type gp2 --enable-ssm --ssh-access --instance-name lw-eks-workernode --managed --kubeconfig Cluster1.config

aws eks list-clusters

kubectl --kubeconfig=Cluster1.config get nodes


kubectl.exe create deployment myd --image=vimal13/apache-webserver-php  --kubeconfig=Cluster1.config
kubectl.exe expose deployment myd --type LoadBalancer --port 80  --kubeconfig=Cluster1.config
kubectl.exe  scale deployment myd --replicas=3  --kubeconfig=Cluster1.config
kubectl.exe get pods --kubeconfig=Cluster1.config 
kubectl.exe  get svc  --kubeconfig=Cluster1.config 




# https://github.com/kubernetes-sigs/aws-ebs-csi-driver/blob/master/docs/install.md#set-up-driver-permissions

kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=release-1.35"  --kubeconfig=Cluster1.config
kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver  --kubeconfig=Cluster1.config

# vim sc.yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-gp3
provisioner: ebs.csi.aws.com
parameters:
  type: gp3
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer


  
  
# vim pvc.yml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: demo-ebs-volume-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: ebs-gp3
  resources:
    requests:
      storage: 10Gi

	  
	  
vim pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: app1
spec:
  containers:
  - name: app1
    image: centos
    command: ["/bin/sh"]
    args: ["-c", "while true; do echo $(date -u) >> /data/out.txt; sleep 5; done"]
    volumeMounts:
    - name: persistent-storage
      mountPath: /data
  volumes:
  - name: persistent-storage
    persistentVolumeClaim:
      claimName: demo-ebs-volume-claim


	
	  
# kubectl describe configmap aws-auth -n kube-system --kubeconfig=Cluster1.config
Add IAM ec2admin policy

# kubectl delete pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver  --kubeconfig=Cluster1.config

# kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver  --kubeconfig=Cluster1.config





https://joachim8675309.medium.com/eks-ebs-storage-with-eksctl-3e526f534215
https://repost.aws/knowledge-center/eks-api-server-unauthorized-error
https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html
https://docs.fuga.cloud/cloud/extra/install-wordpress-in-k8s-with-bitnami-helm/#installing-the-wordpress-chart





https://github.com/eksctl-io/eksctl/blob/main/examples/01-simple-cluster.yaml


