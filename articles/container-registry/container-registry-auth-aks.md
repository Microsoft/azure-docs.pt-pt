---
title: Autenticar com o registo de contentores do Azure de serviço Kubernetes do Azure
description: Saiba como fornecer acesso a imagens no seu registo de contentor privadas do serviço de Kubernetes do Azure ao utilizar um principal de serviço do Azure Active Directory.
services: container-service
author: dlepow
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.openlocfilehash: 1d7e130d619f580aeb82939e19ea5abf680ff039
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333621"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticar com o registo de contentores do Azure de serviço Kubernetes do Azure

Ao utilizar o Azure Container Registry (ACR) com o Azure Kubernetes Service (AKS), um mecanismo de autenticação deve ser estabelecido. Este artigo detalha as configurações recomendadas para a autenticação entre esses dois serviços do Azure.

Este artigo pressupõe que já criou um cluster do AKS e conseguir aceder ao cluster com o `kubectl` cliente da linha de comandos. 

## <a name="grant-aks-access-to-acr"></a>Acesso de AKS de concessão para o ACR

Quando cria um cluster do AKS, o Azure também cria um serviço principal para suportar a capacidade de operação de cluster com outros recursos do Azure. Pode utilizar este principal de serviço gerado automaticamente para a autenticação com um registo do ACR. Para tal, tem de criar um Azure AD [atribuição de função](../role-based-access-control/overview.md#role-assignments) que concede acesso de principal de serviço do cluster para o registo de contentor.

Utilize o seguinte script para conceder o acesso de extração principal de serviço gerado pelo AKS para um Azure container registry. Modificar a `AKS_*` e `ACR_*` variáveis para o seu ambiente antes de executar o script.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Acesso com o segredo do Kubernetes

Em alguns casos, poderá não conseguir atribuir a função necessária para o principal de serviço AKS gerado automaticamente-conceder acesso para o ACR. Por exemplo, devido ao modelo de segurança da sua organização, não poderá ter permissões suficientes no seu inquilino do Azure Active Directory para atribuir uma função ao principal de serviço gerado pelo AKS. Atribuir uma função a um principal de serviço requer a sua conta do Azure AD de ter permissão de escrita para o inquilino do Azure AD. Se não tiver permissão, pode criar um novo principal de serviço e depois conceder acesso para o registo de contentores com um segredo de solicitação de imagem do Kubernetes.

Utilize o seguinte script para criar um novo principal de serviço (que irá utilizar as respetivas credenciais para o segredo de solicitação de imagem de Kubernetes). Modificar o `ACR_NAME` variável para o seu ambiente antes de executar o script.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Agora pode armazenar credenciais do principal de serviço no Kubernetes [segredo de solicitação de imagem][image-pull-secret], que o cluster do AKS fará referência ao contentores em execução.

Utilize o seguinte procedimento **kubectl** comando para criar o Kubernetes secreta. Substitua `<acr-login-server>` com o nome completamente qualificado do seu registo de contentor do Azure (está no formato "acrname.azurecr.io"). Substitua `<service-principal-ID>` e `<service-principal-password>` com os valores que obteve ao executar o script anterior. Substitua `<email-address>` com qualquer endereço de e-mail bem formado.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Agora, pode utilizar o segredo do Kubernetes em implementações de pod especificando seu nome (neste caso, "autenticação do acr") no `imagePullSecrets` parâmetro:

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
