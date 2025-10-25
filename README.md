# APP Deployment with EKS  
Combination of more K8s concepts  
Requirements: aws version, eksctl version.  

$ aws configure  
Enter access key, pass, region  
  
Fargate - serverless compute managed by EKS, No need ec2 when using fargate which will best for practicing and all because managed by itself.  
  
$ eksctl create cluster --name <cluster-name> --region <region-name> --fargate     // create eks cluster  
$ aws eks update-kubeconfig --name <cluster-name> --region <region-name>           // providing k8s to EKS   
$ eksctl create fargateprofile --cluster demo-cluster --region us-east-1 --name alb-sample-app --namespace game-2048     
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-   controller/v2.5.4/docs/examples/2048/2048_full.yaml   //contains necessary yaml file   

*/ Ingress -> service -> deployment   
'Deployment' has label name, 'Service' has selector name and it should be match with label name of deployment podcontainer port of   deployment equals service target port, service port drects to pod(Deployment) and pod listens on its port, Both should be same. 'Ingress'   have service name so its link with service. */  

!!! Wrote ingress.yaml file for now but Need ingress controller, If not nothing happens !!!!   

$ kubectl get svc -n game-2048   
Output:  
NAME            TYPE       CLUSTER-IP       EXTERNAL-IP    PORT        AGE  
service...     NodePort    10.100....        <none>      80/30001/TCP   30s  
// Here External IP is none means, Who have access to aws inside vpc can access resources using nodeport 300001, But not outside if want to access outside the cluster through by ingress //   
  
$ kubectl get ingress-game-3048    
Output:   
NAME         CLASS     HOSTS      ADDRESS    PORTS      AGE    
Ingress....  alb        *            -        80        115s  
// Here address is none means There is no ingress controller(No LB) till now, By using address we can make it to the outside world //    
   
!! Ingress Controller -> Ingress Resources -> Load Balancer -> TargetGroup -> Port    
Ingress Controller access ingress resources and it creates Load Balancer, where LB should be configured with target group,port etc.     
    
!!!   Common steps to link EKS & AWS   !!!    
$  eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve  // link EKS & AWS IAM, make securly to access AWS     resources //    
     
- Setting up ALB : -    
- 
$ curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json     
   
create IAM Policy :      
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json      
     
create IAM Role :     
$ eksctl create iamserviceaccount \     
  --cluster=<your-cluster-name> \    
  --namespace=kube-system \     
  --name=aws-load-balancer-controller \     
  --role-name AmazonEKSLoadBalancerControllerRole \      
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \     
  --approve    
   
    
- Deploy ALB controller : -     
     
Add helm repo :     
$ helm repo add eks https://aws.github.io/eks-charts     
      
Update the repo :     
$ helm repo update eks     
     
Install :      
$ helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \   
  --set clusterName=<your-cluster-name> \    
  --set serviceAccount.create=false \   
  --set serviceAccount.name=aws-load-balancer-controller \    
  --set region=<your-region> \    
  --set vpcId=<your-vpc-id>    
     
Verify the deployment is runnning :     
$ kubectl get deployment -n kube-system aws-load-balancer-controller     
     
     
$ kubectl get ingress -n game-2048    
Can see address & check with "http://<ingress-address>    







