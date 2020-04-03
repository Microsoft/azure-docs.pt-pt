---
title: Integrar o Registo de Contentores Azure com o Serviço Azure Kubernetes
description: Saiba como integrar o Serviço Azure Kubernetes (AKS) com o Registo de Contentores Azure (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 514cc25e1959145c65fe60cd3054cec4ed28f44d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617413"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticar com o Azure Container Registry a partir do Azure Kubernetes Service

Quando estiver a utilizar o Registo de Contentores Azure (ACR) com o Serviço Azure Kubernetes (AKS), é necessário estabelecer um mecanismo de autenticação. Este artigo fornece exemplos para configurar a autenticação entre estes dois serviços Azure. 

Você pode configurar a integração AKS para ACR em alguns comandos simples com o Azure CLI. Esta integração atribui a função AcrPull ao principal de serviço associado ao Cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Estes exemplos requerem:

* **Papel de administrador de** conta Azure ou **Azure** na subscrição do **Azure**
* Versão Azure CLI 2.0.73 ou mais tarde

Para evitar a necessidade de uma função de administrador de conta **Proprietário** ou **Azure,** pode configurar manualmente um diretor de serviço ou utilizar um diretor de serviço existente para autenticar a ACR a partir da AKS. Para mais informações, consulte a [autenticação ACR com os diretores](../container-registry/container-registry-auth-service-principal.md) de serviço ou [authenticaa a partir de Kubernetes com um segredo](../container-registry/container-registry-auth-kubernetes.md)de pull .

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Criar um novo cluster AKS com integração ACR

Você pode configurar a integração AKS e ACR durante a criação inicial do seu cluster AKS.  Para permitir que um cluster AKS interaja com a ACR, é utilizado um diretor de **serviço** de Diretório Ativo Azure. O seguinte comando CLI permite autorizar um ACR existente na sua subscrição e configura rumit a função **ACRPull** apropriada para o diretor de serviço. Forneça valores válidos para os seus parâmetros abaixo.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Em alternativa, pode especificar o nome ACR utilizando um ID de recurso ACR, que tem o seguinte formato:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

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

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Também pode remover a integração entre um ACR e um cluster AKS com o seguinte

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

ou

```azurecli
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

```yaml
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

```console
kubectl apply -f acr-nginx.yaml
```

Pode monitorizar a implantação executando:

```console
kubectl get pods
```

Devias ter duas cápsulas de corrida.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
