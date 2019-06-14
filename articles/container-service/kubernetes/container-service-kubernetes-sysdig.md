---
title: (PRETERIDO) Monitorizar o cluster de Kubernetes do Azure - Sysdig
description: Monitorização de cluster do Kubernetes no Azure Container Service com o Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4aef241e2c86e4016c3c468fcdcfdfc620fc7aa9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309274"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(PRETERIDO) Monitorizar um cluster de Kubernetes do Azure Container Service com o Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo pressupõe que tenha [criado um cluster de Kubernetes com o Azure Container Service](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem as ferramentas do azure de cli e o kubectl instaladas.

Pode testar se tiver o `az` ferramenta instalada executando:

```console
$ az --version
```

Se não tiver o `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).

Pode testar se tiver o `kubectl` ferramenta instalada executando:

```console
$ kubectl version
```

Se não tiver `kubectl` instalado, pode executar:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
O Sysdig é uma monitorização externos como uma empresa de serviço que pode monitorizar contentores no cluster do Kubernetes em execução no Azure. Usar o Sysdig requer uma conta ativa do Sysdig.
Pode inscrever-se para uma conta respetivo [site](https://app.sysdigcloud.com).

Após ter iniciado sessão no site na nuvem do Sysdig, clique no seu nome de utilizador e deverá ver a sua "Chave de Acesso" na página. 

![Chave de API do Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalar os agentes do Sysdig no Kubernetes
Para monitorizar os seus contentores, o Sysdig é executado um processo em cada máquina com o Kubernetes `DaemonSet`.
DaemonSets são objetos de API do Kubernetes que executam uma única instância de um contentor de por máquina.
Eles são perfeitos para instalar ferramentas como o agente de monitorização do Sysdig.

Para instalar o daemonset do Sysdig, deve primeiro baixar [o modelo](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) do sysdig. Salve esse arquivo como `sysdig-daemonset.yaml`.

No Linux e OS X pode executar:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

No PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Em seguida, edite esse arquivo para inserir a chave de acesso, que obteve na sua conta do Sysdig.

Por fim, crie o DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Ver a monitorização
Depois de instalado e em execução, os agentes devem puxar dados para o Sysdig.  Volte para o [sysdig dashboard](https://app.sysdigcloud.com) e deverá ver informações sobre os contentores.

Também pode instalar dashboards de Kubernetes específicos através da [Assistente de novo dashboard](https://app.sysdigcloud.com/#/dashboards/new).
