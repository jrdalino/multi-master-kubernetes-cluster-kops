# multi-master-kubernetes-cluster-kops

## Prerequisites
- kubectl
- kops
```
$ mv kops-darwin-amd64 /usr/local/bin/kops
$ chmod +x /usr/local/bin/kops
$ kops version Version 1.5.1 (git-01deca8)
```

## Create Route 53 hosted zone
- DNS using example.k8s.local

```
$ aws route53 create-hosted-zone \
--name mycluster.k8s.local \
--vpc VPCRegion=us-east-1,VPCId=vpc-12345678 \
--caller-reference 2014-04-01-18:47 \
--hosted-zone-config Comment=create-private-dns-stack,PrivateZone=true
```

## Create S3 bucket to store the cluster state
```
$ aws s3 mb s3://jrdalino-calculator-kubernetes-state
```

## Create the Kubernetes Cluster
```
$ kops create cluster \
       --state "s3://jrdalino-calculator-kubernetes-state" \
       --zones "us-east-1a,us-east-1b,us-east-1c"  \
       --master-count 3 \
       --master-size=t2.micro \
       --node-count 2 \
       --node-size=t2.micro \
       --name mycluster.k8s.local \
       --yes
```

## Validate the Kubernetes Cluster
```
$ kops validate cluster \
       --state "s3://jrdalino-calculator-kubernetes-state" \
       --name mycluster.k8s.local
```

## (Cleanup)
```
$ kops delete cluster \
       --state "s3://jrdalino-calculator-kubernetes-state" \
       --name mycluster.k8s.local \
       --yes
```
