# kops-k8s-demo

- install kops

curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops

- install kubectl

curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

- create amazon s3

aws s3api create-bucket --bucket $BUCKET_NAME_123456

- versioning amazon s3

aws s3api put-bucket-versioning --bucket $BUCKET_NAME_123456 --versioning-configuration Status=Enabled

- export s3 bucket to kops

export KOPS_STATE_STORE=s3://$BUCKET_NAME_123456
