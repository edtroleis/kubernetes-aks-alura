### Alura aks
```
https://github.com/alura-cursos/Alura-AKS

Aks management is abstracted in Azure.
We can change node numbers without affect service availability.
Changing node size affect availability.
In aks we can't access master only workers.

Service Principal is the identity used by application to resource management in Azure.
Instead of using username/password Azure applications use Service Principal with  ClientID e ClientSecret.
Service Principal identity will be used to create virtual machines and resources in the aks network.
In creation and sizing of virtual machines and resources are done through Service Principal identity used instead of Azure administrator account.
```

## aks commands
```
az login --username <username>
az account list
az account set --subscription="Id"
az aks get-credentials --name aksClusterExample --resource-group kubernetes-example

# Check kubernetes current context
kubectl config current-context

# Delete cluster
az aks delete --resource-group kubernetes-example --name aksClusterExample --no-wait
```

# Create kubernetes resources
```
kubectl create -f ./db/permissoes.yaml
kubectl create -f ./db/statefulset.yaml
kubectl create -f ./db/servico-banco.yaml
kubectl create -f ./web/deployment.yaml
kubectl create -f ./web/servico-aplicacao.yaml

kubectl get nodes --all-namespaces
```

## Configure database
```
kubectl get pods
kubectl exec -it <statefulset-mysql> sh

mysql -u root
use loja

create table produtos (id integer auto_increment primary key, nome varchar(255), preco decimal(10,2));
alter table produtos add column usado boolean default false;
alter table produtos add column descricao varchar(255);
create table categorias (id integer auto_increment primary key, nome varchar(255));
insert into categorias (nome) values ("Futebol"), ("Volei"), ("Tenis");
alter table produtos add column categoria_id integer;
update produtos set categoria_id = 1;

minikube service servico-aplicacao --url
```

## Check storage classes
```
kubectl get storageclass
```


# Azure Container registry (ACR)
```
docker pull username/image:version
docker tag username/image:version azureregistryname.azurecr.io/image:version
az acr login --name azureregistryname
docker push azureregistryname.azurecr.io/image:version
```

# Create secret
```
kubectl create secret docker-registry azureregistryname.secret --docker-server azureregistryname.azurecr.io --docker-username <username> --docker-password <password> --docker-email edtroleis@outlook.com
```

# Update container
```
kubectl apply -f ./acr/web/deployment.yaml
kubectl get pods

kubectl get services

access <external_ip> from browser
```


# Create aks using Azure cli
```
# Resource group
az group create --name aluracli-rg --location easus
az group list --output table

# aks
az aks get-versions --location eastus --output table
az aks create --name aluracli-k8s --kubernetes-version "1.16.10" --node-count 4 --resource-group aluracli-rg --location eastus --generate-ssh-keys
az aks list --output table

# acr
az acr create --name aluracliregistry --resource-group aluracli-rg --sku Basic --location eastus

az acr login --name aluracliregistry
docker image ls
docker tag rafanercessian/aplicacao-loja:v1 aluracliregistry.azurerc.io/loja/aplicacao-loja:v1
docker push aluracliregistry.azurerc.io/loja/aplicacao-loja:v1

az acr credential -h
az acr update -n aluracliregistry --admin-enabled true
az acr credential show --name aluracliregistry

# get credentials to create secret
az aks get-credentials --name aluracli-k8s --resource-group aluracli-rg

# create secret
kubectl create secret docker-registry aluracliregristry.secret --docker-server aluracliregistry.azurecr.io --docker-username aluracliregistry --dockerpassword <password> --docker-email edtroleis@outlook.com

kubectl create -f ./permissoes.yaml
kubectl create -f ./statefulset-mysql.yaml
kubectl create -f ./servico.yaml
```
