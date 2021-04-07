---
title: Integre o Registo de Contentores Azure com o Serviço Azure Kubernetes
description: Saiba como integrar o Serviço Azure Kubernetes (AKS) com o Registo de Contentores Azure (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: 19ece696dabc81e643e8a904d506d22e40eaa099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499157"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticar com o Azure Container Registry a partir do Azure Kubernetes Service

Quando estiver a utilizar o Registo de Contentores Azure (ACR) com o Serviço Azure Kubernetes (AKS), é necessário estabelecer um mecanismo de autenticação. Esta operação é implementada como parte da experiência CLI e Portal, concedendo as permissões necessárias ao seu ACR. Este artigo fornece exemplos para configurar a autenticação entre estes dois serviços Azure. 

Pode configurar a integração AKS para ACR em alguns comandos simples com o Azure CLI. Esta integração atribui o papel de AcrPull à identidade gerida associada ao Cluster AKS.

> [!NOTE]
> Este artigo abrange a autenticação automática entre AKS e ACR. Se precisar de tirar uma imagem de um registo externo privado, utilize um [segredo de imagem][Image Pull Secret].

## <a name="before-you-begin"></a>Antes de começar

Estes exemplos requerem:

* **Papel de** administrador de conta proprietário ou **Azure** na subscrição do **Azure**
* Versão Azure CLI 2.7.0 ou mais tarde

Para evitar a necessidade de uma função de administrador de conta **Proprietário** ou **Azure,** pode configurar manualmente uma identidade gerida ou utilizar uma identidade gerida existente para autenticar ACR a partir de AKS. Para obter mais informações, consulte [uma identidade gerida pelo Azure para autenticar num registo de contentores Azure](../container-registry/container-registry-authentication-managed-identity.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Criar um novo cluster AKS com integração ACR

Pode configurar a integração AKS e ACR durante a criação inicial do seu cluster AKS.  Para permitir que um cluster AKS interaja com a ACR, é utilizada uma **identidade gerida pelo** Azure Ative. O seguinte comando CLI permite-lhe autorizar um ACR existente na sua subscrição e configura o papel **ACRPull** apropriado para a identidade gerida. Forneça valores válidos para os seus parâmetros abaixo.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Em alternativa, pode especificar o nome ACR utilizando um ID de recurso ACR, que tem o seguinte formato:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Se estiver a utilizar um ACR que esteja localizado numa subscrição diferente do seu cluster AKS, utilize o ID de recursos ACR ao anexar ou separar-se de um cluster AKS.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Este passo pode levar vários minutos a ser concluído.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Integração de ACR configurada para clusters AKS existentes

Integre um ACR existente com clusters AKS existentes fornecendo valores válidos para **acr-name** ou **acr-resource-id** como abaixo.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

ou,

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Também pode remover a integração entre um ACR e um cluster AKS com o seguinte

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

ou

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Trabalhar com a ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importe uma imagem no seu ACR

Importe uma imagem do estivador hub para o seu ACR executando o seguinte:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Implementar a imagem da amostra de ACR para AKS

Certifique-se de que tem as credenciais AKS adequadas

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Crie um ficheiro chamado **acr-nginx.yaml** que contenha o seguinte. Substitua o nome de recurso do seu registo por **acr-name**. Exemplo: *myContainerRegistry*.

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
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Em seguida, execute esta implementação no seu cluster AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Pode monitorizar a implementação executando:

```console
kubectl get pods
```

Devias ter duas cápsulas de corrida.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Resolução de problemas
* Executar o comando [az aks check-acr](/cli/azure/aks#az_aks_check_acr) para validar que o registo está acessível a partir do cluster AKS.
* Saiba mais sobre [ACR Diagnostics](../container-registry/container-registry-diagnostics-audit-logs.md)
* Saiba mais sobre [a ACR Health](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az-aks-create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/