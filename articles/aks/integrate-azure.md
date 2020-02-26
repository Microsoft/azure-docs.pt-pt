---
title: Integração com serviços geridos pelo Azure com o Open Service Broker for Azure (OSBA)
description: Integração com serviços geridos pelo Azure com o Open Service Broker for Azure (OSBA)
author: zr-msft
ms.topic: overview
ms.date: 12/05/2017
ms.author: zarhoads
ms.openlocfilehash: 8d727256afbe152a4f7022d0fd2454c4677b023c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595608"
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integração com serviços geridos pelo Azure com o Open Service Broker for Azure (OSBA)

Juntamente com o [Catálogo de Serviços do Kubernetes][kubernetes-service-catalog], o Open Service Broker for Azure (OSBA) permite aos programadores utilizar serviços geridos pelo Azure no Kubernetes. Este guia foca-se na implementação do Catálogo de Serviços do Kubernetes, do Open Service Broker for Azure (OSBA) e de aplicações que utilizam serviços geridos pelo Azure com o Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure

* Azure CLI: [instale-o localmente,][azure-cli-install]ou use-o na [Casca de Nuvem Azure][azure-cloud-shell].

* Leme CLI 2.7+: [instale-o localmente][helm-cli-install], ou use-o na Casca de [Nuvem Azure][azure-cloud-shell].

* Permissões para criar um principal de serviço com a função de Contribuinte na sua subscrição do Azure

* Um cluster do Serviço Kubernetes do Azure (AKS) existente. Se precisar de um cluster do AKS, siga o início rápido [Criar um cluster do AKS][create-aks-cluster].

## <a name="install-service-catalog"></a>Instalar o Catálogo de Serviços

O primeiro passo é instalar o Catálogo de Serviços no seu cluster do Kubernetes com um gráfico Helm. Atualize a sua instalação do Tiller (servidor Helm) no seu cluster com:

```azurecli-interactive
helm init --upgrade
```

Agora, adicione o gráfico de Catálogo de Serviços ao repositório Helm:

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Por último, instale o Catálogo de Serviços com o gráfico Helm. Se o cluster for compatível com RBAC, execute este comando.

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

Se o cluster não for compatível com RBAC, execute este comando.

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

Após a execução do gráfico Helm, verifique se `servicecatalog` aparece no resultado do seguinte comando:

```azurecli-interactive
kubectl get apiservice
```

Por exemplo, deverá ver um resultado semelhante ao seguinte (aqui é apresentado truncada):

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Instalar o Open Service Broker for Azure

O passo seguinte é instalar o [Open Service Broker for Azure][open-service-broker-azure], que inclui o catálogo dos serviços geridos pelo Azure. Exemplos de serviços do Azure disponíveis: Base de Dados do Azure para PostgreSQL, Base de Dados do Azure para MySQL e Base de Dados SQL do Azure.

Comece por adicionar o Corretor de Serviço aberto para o repositório Azure Helm:

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Crie um [Principal de Serviço][create-service-principal] com o seguinte comando da CLI do Azure:

```azurecli-interactive
az ad sp create-for-rbac
```

O resultado deve ser semelhante ao seguinte. Tome nota dos valores de `appId`, `password` e `tenant`, que irá utilizar no passo seguinte.

```JSON
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

Defina as seguintes variáveis de ambiente com os valores anteriores:

```azurecli-interactive
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

Agora, obtenha o ID da sua subscrição do Azure:

```azurecli-interactive
az account show --query id --output tsv
```

Mais uma vez, defina as seguintes variáveis de ambiente com o valor anterior:

```azurecli-interactive
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

Agora que já preencheu estas variáveis de ambiente, execute o seguinte comando para instalar o Open Service Broker for Azure com o gráfico Helm:

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Quando a implementação do OSBA estiver concluída, instale a [CLI do Catálogo de Serviços][service-catalog-cli], uma interface de linha de comandos fácil de utilizar para consultar mediadores de serviço, classes de serviço, planos de serviço e muito mais.

Execute os comandos seguintes para instalar o binário da CLI do Catálogo de Serviços:

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Agora, liste os mediadores de serviço instalados:

```azurecli-interactive
./svcat get brokers
```

Deverá ver um resultado semelhante ao seguinte:

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Em seguida, liste as classes de serviço disponíveis. As classes de serviço apresentadas são os serviços geridos pelo Azure que podem ser aprovisionados através do Open Service Broker for Azure.

```azurecli-interactive
./svcat get classes
```

Por último, liste todos os planos de serviço disponíveis. Os planos de serviço são os escalões de serviço para os serviços geridos pelo Azure. Por exemplo, para a Base de Dados do Azure para MySQL, os planos vão do `basic50` para o escalão Básico com 50 Unidades de Transação de Base de dados (DTUs) até `standard800` para o escalão Standard com 800 DTUs.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Instalar o WordPress a partir do gráfico Helm com a Base de Dados do Azure para MySQL

Neste passo, vai utilizar o Helm para instalar um gráfico Helm atualizado para o WordPress. O gráfico aprovisiona uma instância externa da Base de Dados do Azure para MySQL que o WordPress pode utilizar. Este processo pode demorar alguns minutos.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0 --set replicaCount=1
```

Para verificar se a instalação aprovisionou os recursos certos, liste as instâncias e enlaces dos serviços instalados:

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Liste os segredos instalados:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou o Catálogo de Serviços num cluster do Serviço Kubernetes do Azure (AKS). Utilizou o Open Service Broker for Azure para implementar uma instalação de WordPress que utiliza os serviços geridos pelo Azure, neste caso, a Base de Dados do Azure para MySQL.

Consulte o repositório [Azure/helm-charts][helm-charts] para aceder a outros gráficos Helm atualizados baseados no OSBA. Se tiver interesse em criar os seus próprios gráficos que funcionam com o OSBA, veja [Criar um Novo Gráfico][helm-create-new-chart].

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: https://docs.helm.sh/helm/#helm-install
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
