---
title: Autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure
description: Saiba como fornecer acesso a imagens em seu registro de contêiner privado do serviço kubernetes do Azure usando uma entidade de serviço Azure Active Directory.
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/27/2019
ms.author: danlep
ms.openlocfilehash: f80956ec401737766f7a85540e90be70b9d621e7
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114692"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure

Quando você estiver usando o ACR (registro de contêiner do Azure) com o AKS (serviço kubernetes do Azure), um mecanismo de autenticação precisará ser estabelecido. Este artigo detalha as configurações recomendadas para autenticação entre esses dois serviços do Azure.

Você só precisa configurar um desses métodos de autenticação. A abordagem mais comum é [conceder acesso usando a entidade de serviço AKs](#grant-aks-access-to-acr). Se você tiver necessidades específicas, poderá, opcionalmente, [conceder acesso usando segredos do kubernetes](#access-with-kubernetes-secret).

Este artigo pressupõe que você já criou um cluster AKs e pode acessar o cluster com o cliente de linha de `kubectl` comando. Se, em vez disso, você quiser criar um cluster e configurar o acesso a um registro de contêiner no [momento da criação do cluster, consulte o tutorial: Implante um cluster](../aks/tutorial-kubernetes-deploy-cluster.md) AKs ou [autentique com o registro de contêiner do Azure do serviço kubernetes do Azure (versão prévia)](../aks/cluster-container-registry-integration.md).

## <a name="grant-aks-access-to-acr"></a>Conceder acesso AKS ao ACR

Quando você cria um cluster AKS, o Azure também cria uma entidade de serviço para dar suporte à operabilidade do cluster com outros recursos do Azure. Você pode usar essa entidade de serviço gerada automaticamente para autenticação com um registro ACR. Para fazer isso, você precisa criar uma [atribuição de função](../role-based-access-control/overview.md#role-assignments) do Azure AD que conceda acesso de entidade de serviço do cluster ao registro de contêiner.

Use o script a seguir para conceder o acesso de pull da entidade de serviço gerada pelo AKS a um registro de contêiner do Azure. Modifique as `AKS_*` variáveis `ACR_*` e para o seu ambiente antes de executar o script.

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

## <a name="access-with-kubernetes-secret"></a>Acesso com segredo kubernetes

Em alguns casos, talvez você não possa atribuir a função necessária à entidade de serviço AKS gerada automaticamente, concedendo-lhe acesso ao ACR. Por exemplo, devido ao modelo de segurança de sua organização, talvez você não tenha permissões suficientes em seu locatário Azure Active Directory para atribuir uma função à entidade de serviço gerada pelo AKS. A atribuição de uma função a uma entidade de serviço requer que sua conta do Azure AD tenha permissão de gravação para seu locatário do Azure AD. Se você não tiver permissão, poderá criar uma nova entidade de serviço e, em seguida, conceder acesso ao registro de contêiner usando um segredo de pull de imagem kubernetes.

Use o script a seguir para criar uma nova entidade de serviço (você usará suas credenciais para o segredo de pull da imagem kubernetes). Modifique a `ACR_NAME` variável para seu ambiente antes de executar o script.

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

Agora você pode armazenar as credenciais da entidade de serviço em um [segredo de pull de imagem][image-pull-secret]kubernetes, que seu cluster AKs fará referência ao executar contêineres.

Use o comando **kubectl** a seguir para criar o segredo kubernetes. Substitua `<acr-login-server>` pelo nome totalmente qualificado do seu registro de contêiner do Azure (ele está no formato "acrname.azurecr.Io"). Substitua `<service-principal-ID>` e`<service-principal-password>` pelos valores obtidos executando o script anterior. Substitua `<email-address>` por qualquer endereço de email bem formado.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Agora você pode usar o segredo kubernetes em implantações de Pod especificando seu nome (nesse caso, "ACR-auth") no `imagePullSecrets` parâmetro:

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
