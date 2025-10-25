# APP Deployment with EKS
Combination of more K8s concepts
Requirements: aws version, eksctl version.

$ aws configure
Enter access key, pass, region

Fargate - serverless compute managed by EKS, No need ec2 when using fargate which will best for practicing and all because managed by itself.

$ eksctl create cluster --name <cluster-name> --region <region-name> --fargate     // create eks cluster
$ aws eks update-kubeconfig --name <cluster-name> --region <region-name>           // providing k8s to EKS
$ eksctl create fargateprofile --cluster demo-cluster --region us-east-1 --name alb-sample-app --namespace game-2048  
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml   //contains necessary yaml file





