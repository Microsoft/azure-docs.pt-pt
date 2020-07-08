---
title: (PRECADO) Monitor Azure Kubernetes cluster - Sysdig
description: Monitorização do cluster Kubernetes no Serviço de Contentores Azure utilizando sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371104"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(PRECADO) Monitorize um cluster de serviço de contentores Azure Kubernetes usando Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough assume que [criou um cluster Kubernetes usando o Serviço de Contentores Azure.](container-service-kubernetes-walkthrough.md)

Também assume que tem as ferramentas azure cli e kubectl instaladas.

Pode testar se a ferramenta está `az` instalada em execução:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).

Pode testar se a ferramenta está `kubectl` instalada em execução:

```console
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
A Sysdig é uma monitorização externa como uma empresa de serviços que pode monitorizar contentores no seu cluster Kubernetes em funcionamento em Azure. A utilização do Sysdig requer uma conta Sysdig ativa.
Pode inscrever-se numa conta no [site](https://app.sysdigcloud.com)deles.

Após ter iniciado sessão no site na nuvem do Sysdig, clique no seu nome de utilizador e deverá ver a sua "Chave de Acesso" na página. 

![Chave de API do Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalação dos agentes Sysdig em Kubernetes
Para monitorizar os seus recipientes, a Sysdig executa um processo em cada máquina utilizando um Kubernetes `DaemonSet` .
Os DaemonSets são objetos API da Kubernetes que executam uma única instância de um recipiente por máquina.
São perfeitos para instalar ferramentas como o agente de monitorização do Sysdig.

Para instalar o daemonset Sysdig, deve primeiro descarregar [o modelo](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) do sysdig. Guarde o ficheiro como `sysdig-daemonset.yaml` .

No Linux e NOS X pode correr:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

No PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Em seguida edite esse ficheiro para inserir a chave de acesso, que obteve na sua conta Sysdig.

Finalmente, crie o DaemonSet:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Veja a sua monitorização
Uma vez instalados e em funcionamento, os agentes devem bombear os dados de volta para Sysdig.  Volte para o [painel de sysdig](https://app.sysdigcloud.com) e deve ver informações sobre os seus recipientes.

Também pode instalar dashboards específicos de Kubernetes através do novo assistente do painel de [instrumentos](https://app.sysdigcloud.com/#/dashboards/new).
