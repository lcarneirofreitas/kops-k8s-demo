# kops-k8s-demo


- export variables
```
export BUCKET_NAME=name-bucket-s3
export DOMAIN_NAME=name-domain-dns
```


- install kops
```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
```

- install kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

- create amazon s3
```
aws s3api create-bucket --bucket $BUCKET_NAME
```

- versioning amazon s3
```
aws s3api put-bucket-versioning --bucket $BUCKET_NAME --versioning-configuration Status=Enabled
```

- export s3 bucket to kops
```
export KOPS_STATE_STORE=s3://$BUCKET_NAME
```

- configure dns route53 amazon
```
ID=$(uuidgen) && \
aws route53 create-hosted-zone \
--name $DOMAIN_NAME \
--caller-reference $ID
```

- create cluster kubernetes kops
```
kops create cluster \
    --name $DOMAIN_NAME \
    --node-count 1 \
    --zones us-east-1a \
    --master-zones us-east-1a \
    --dns-zone $DOMAIN_NAME \
    --state s3://$BUCKET_NAME \
    --topology private \
    --networking calico \
    --bastion \
    --yes
```

- validating cluster creation
```
kops validate cluster --state s3://$BUCKET_NAME --name $DOMAIN_NAME
kubectl get nodes --show-labels
ssh -A -i ~/.ssh/id_rsa admin@bastion.$DOMAIN_NAME
kubectl -n kube-system get pods
```
