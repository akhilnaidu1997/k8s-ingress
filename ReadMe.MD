1. Create an IAM OIDC provider
```
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster cluster-26 --approve
```
2. Download an IAM policy for the LBC
```
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.12.0/docs/install/iam_policy.json
```
3. Create an IAM policy named AWSLoadBalancerControllerIAMPolicy
```
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam-policy.json
```
4. Create an IAM role and Kubernetes ServiceAccount for the LBC
```
eksctl create iamserviceaccount \
--cluster=cluster-26 \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve
```
Add Controller to cluster:

5. Install helm
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
chmod 700 get_helm.sh
./get_helm.sh
```

6. Add the EKS chart repo to Helm
```
helm repo add eks https://aws.github.io/eks-charts
```
7. Helm install command for clusters with IRSA:
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=<cluster-name> --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
```
check if drivers are running
```
kubectl get pods -n kube-system
```

test