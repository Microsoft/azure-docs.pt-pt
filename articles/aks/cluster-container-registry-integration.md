---
title: Integrar o registro de contêiner do Azure com o serviço kubernetes do Azure
description: Saiba como integrar o AKS (serviço de kubernetes do Azure) com o ACR (registro de contêiner do Azure)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: ba52cac4ebe923b7217550ed90948d908d8daf7f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73900656"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure

Quando você estiver usando o ACR (registro de contêiner do Azure) com o AKS (serviço kubernetes do Azure), um mecanismo de autenticação precisará ser estabelecido. Este artigo fornece exemplos para configurar a autenticação entre esses dois serviços do Azure.

Você pode configurar o AKS para a integração de ACR em alguns comandos simples com o CLI do Azure.

## <a name="before-you-begin"></a>Antes de começar

Estes exemplos exigem:

* Função de **administrador da conta do Azure** ou **proprietário** na **assinatura do Azure**
* CLI do Azure versão 2.0.73 ou posterior

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Criar um novo cluster AKS com integração com ACR

Você pode configurar a integração de AKS e ACR durante a criação inicial do cluster AKS.  Para permitir que um cluster AKS interaja com o ACR, é usada uma **entidade de serviço** Azure Active Directory. O comando da CLI a seguir permite autorizar um ACR existente em sua assinatura e configurar a função **ACRPull** apropriada para a entidade de serviço. Forneça valores válidos para os parâmetros abaixo. 
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR

```
Como alternativa, você pode especificar o nome do ACR usando uma ID de recurso ACR, que tem o seguinte formato:

/subscriptions/\<Subscription-ID\>/resourceGroups/\<Resource-Group-Name\>/providers/Microsoft.ContainerRegistry/registries/\<Name\> 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Esta etapa pode levar vários minutos para ser concluída.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurar a integração do ACR para clusters AKS existentes

Integre um ACR existente a clusters AKS existentes fornecendo valores válidos para **ACR-Name** ou **ACR-Resource-ID** como mostrado abaixo.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
or
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Você também pode remover a integração entre um ACR e um cluster AKS com o seguinte
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
ou
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Trabalhando com ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importar uma imagem para o ACR

Importe uma imagem do Hub do Docker para o ACR executando o seguinte:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Implantar a imagem de exemplo do ACR para o AKS

Verifique se você tem as credenciais de AKS adequadas

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Crie um arquivo chamado **ACR-nginx. YAML** que contenha o seguinte:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

Em seguida, execute esta implantação em seu cluster AKS:
```
kubectl apply -f acr-nginx.yaml
```

Você pode monitorar a implantação executando:
```
kubectl get pods
```
Você deve ter dois pods em execução.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
