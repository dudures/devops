# AWS CLI
https://docs.aws.amazon.com/pt_br/cli/latest/userguide/cli-services-ec2-instances.html

Script para lançar as máquinas direto sem usar o console.

Somente pegar os IP's publicos.

```sh
# RANCHER-SERVER - ami-0ee02acd56a52998e (64-bit x86)
# CLUSTER -  Processador ARM - ami-0ea142bd244023692
# t3a.micro - 0,0094 USD por hora
# t3.medium	- 0,0416 USD por hora
# devops-ninja
# sg-06399c7c14d9800bc
# subnet-01683e9334dbdaa83

#Criação chave pela AWS CLI
aws ec2 create-key-pair --key-name AwsEduardoDevops --query 'KeyEduardo' --output text > AwsEduardoDevops.pem

# RANCHER SERVER - AWS Eduardo

$ aws ec2 run-instances --image-id ami-0ee02acd56a52998e --count 1 --instance-type t3.medium --key-name AwsEduardoDevops --security-group-ids sg-0783b26e30b51e91b --subnet-id subnet-01683e9334dbdaa83 --user-data file://rancher.sh --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=rancherserver}]' 'ResourceType=volume,Tags=[{Key=Name,Value=rancherserver}]' 

# RANCHER SERVER
$ aws ec2 run-instances --image-id ami-0dba2cb6798deb6d8 --count 1 --instance-type t3.medium --key-name curso --security-group-ids sg-06399c7c14d9800bc --subnet-id subnet-3bf89867 --user-data file://rancher.sh --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=rancherserver}]' 'ResourceType=volume,Tags=[{Key=Name,Value=rancherserver}]' 


# MASTER
$ aws ec2 run-instances --image-id ami-0dba2cb6798deb6d8 --count 1 --instance-type t3.medium --key-name curso --security-group-ids sg-06399c7c14d9800bc --subnet-id subnet-3bf89867 --user-data file://k3s.sh --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=k8s-master}]' 'ResourceType=volume,Tags=[{Key=Name,Value=k8s-master}]'

## Entrar na instância pelo console da aws e rodar no shell como sudo:
# cat /var/lib/rancher/k3s/server/node-token
# Pegar o token e fazer a string para rodar no arquivo k3s-node.sh

# NODE X
$ aws ec2 run-instances --image-id ami-0dba2cb6798deb6d8 --count 5 --instance-type t3.medium --key-name curso --security-group-ids sg-06399c7c14d9800bc --subnet-id subnet-3bf89867 --user-data file://k3s-node.sh --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=k8s-node}]' 'ResourceType=volume,Tags=[{Key=Name,Value=k8s-node}]'

# Importar o cluster para dentro do Rancher, pegando a string de conexão que é gerada quando importamos o cluster.

$ curl --insecure -sfL https://3.238.150.14/v3/import/gfh8dlj62wsfq6xr4xdjzmrnldfn6jtgzhfv2glhhpxz88nw6thg2b.yaml | k3s kubectl apply -f -

```