---
title: Integrar o registro de contêiner do Azure com o serviço kubernetes do Azure
description: Saiba como integrar o AKS (serviço de kubernetes do Azure) com o ACR (registro de contêiner do Azure)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: 3c11367945b74db9be20ade86c7bc26901440e4d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305152"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Visualização-autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure

Quando você estiver usando o ACR (registro de contêiner do Azure) com o AKS (serviço kubernetes do Azure), um mecanismo de autenticação precisará ser estabelecido. Este artigo detalha as configurações recomendadas para autenticação entre esses dois serviços do Azure.

Você pode configurar o AKS para a integração de ACR em alguns comandos simples com o CLI do Azure.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS](support-policies.md)
> * [Perguntas frequentes do suporte do Azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Você deve ter o seguinte:

* Função de **administrador da conta do Azure** ou **proprietário** na **assinatura do Azure**
* Você também precisa do CLI do Azure versão 2.0.70 ou posterior e a extensão 0.4.8 AKs-Preview
* Você precisa do [Docker instalado](https://docs.docker.com/install/) em seu cliente e precisa ter acesso ao [Hub do Docker](https://hub.docker.com/)

## <a name="install-latest-aks-cli-preview-extension"></a>Instalar a última extensão de visualização da CLI do AKS

Você precisa da extensão **0.4.13 AKs-Preview** ou posterior.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Criar um novo cluster AKS com integração com ACR

Você pode configurar a integração de AKS e ACR durante a criação inicial do cluster AKS.  Para permitir que um cluster AKS interaja com o ACR, é usada uma **entidade de serviço** Azure Active Directory. O comando da CLI a seguir permite autorizar um ACR existente em sua assinatura e configurar a função **ACRPull** apropriada para a entidade de serviço. Forneça valores válidos para os parâmetros abaixo.  Os parâmetros entre colchetes são opcionais.
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
\* * Uma ID de recurso ACR tem o seguinte formato: 

/subscriptions/< Subscription-d >/resourceGroups/< Resource-Group-Name >/providers/Microsoft.ContainerRegistry/registries/{name} 
  
Esta etapa pode levar vários minutos para ser concluída.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurar a integração do ACR para clusters AKS existentes

Integre um ACR existente a clusters AKS existentes fornecendo valores válidos para **ACR-Name** ou **ACR-Resource-ID** como mostrado abaixo.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Você também pode remover a integração entre um ACR e um cluster AKS com o seguinte
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>Faça logon em seu ACR

Use o comando a seguir para fazer logon em seu ACR.  Substitua o <acrname> parâmetro pelo seu nome de ACR.  Por exemplo, o padrão é **aks < seu grupo de recursos > ACR**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Efetuar pull de uma imagem do Hub do Docker e enviar por push para o ACR

Efetuar pull de uma imagem do Hub do Docker, marcar a imagem e enviar por push para o ACR.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Atualizar o estado e verificar pods

Execute as etapas a seguir para verificar sua implantação.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Exiba o arquivo YAML e edite a propriedade Image substituindo o valor pelo seu servidor de logon do ACR, imagem e marca.

```
$ cat acr-nginx.yaml

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

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
