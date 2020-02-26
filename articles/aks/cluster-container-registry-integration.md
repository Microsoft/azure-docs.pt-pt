---
title: Integrar o Registo de Contentores Azure com o Serviço Azure Kubernetes
description: Saiba como integrar o Serviço Azure Kubernetes (AKS) com o Registo de Contentores Azure (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 09/17/2018
ms.openlocfilehash: b1f4449728589eca4f64035d7e70d01dbc187bc4
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596203"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticação com Registo de Contentores Azure do Serviço Azure Kubernetes

Quando estiver a utilizar o Registo de Contentores Azure (ACR) com o Serviço Azure Kubernetes (AKS), é necessário estabelecer um mecanismo de autenticação. Este artigo fornece exemplos para configurar a autenticação entre estes dois serviços Azure.

Você pode configurar a integração AKS para ACR em alguns comandos simples com o Azure CLI.

## <a name="before-you-begin"></a>Antes de começar

Estes exemplos requerem:

* **Papel de administrador de** conta Azure ou **Azure** na subscrição do **Azure**
* Versão Azure CLI 2.0.73 ou mais tarde

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Criar um novo cluster AKS com integração ACR

Você pode configurar a integração AKS e ACR durante a criação inicial do seu cluster AKS.  Para permitir que um cluster AKS interaja com a ACR, é utilizado um diretor de **serviço** de Diretório Ativo Azure. O seguinte comando CLI permite autorizar um ACR existente na sua subscrição e configura rumit a função **ACRPull** apropriada para o diretor de serviço. Forneça valores válidos para os seus parâmetros abaixo. 
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR

```
Em alternativa, pode especificar o nome ACR utilizando um ID de recurso ACR, que tem o seguinte formato:

/subscrições/\<\>de subscrição/recursosGroups/\<nome de grupo de recursos\>/fornecedores/Microsoft.ContainerRegistry/registros/\<nome\> 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Este passo pode levar alguns minutos para ser concluído.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configure a integração aCR para os aglomerados AKS existentes

Integre um ACR existente com os aglomerados AKS existentes, fornecendo valores válidos para **o nome acr** ou **acr-resource-id** como abaixo.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
ou,
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Também pode remover a integração entre um ACR e um cluster AKS com o seguinte
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
ou
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Trabalhar com a ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importe uma imagem para o seu ACR

Importe uma imagem do centro de estivador para o seu ACR executando o seguinte:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Implementar a imagem da amostra de ACR para AKS

Certifique-se de que tem as credenciais AKS adequadas

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Crie um ficheiro chamado **acr-nginx.yaml** que contenha o seguinte:

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

Em seguida, execute esta implantação no seu cluster AKS:
```
kubectl apply -f acr-nginx.yaml
```

Pode monitorizar a implantação executando:
```
kubectl get pods
```
Devias ter duas cápsulas de corrida.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
