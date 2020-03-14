---
title: (DEPRECIADO) Monitor Azure Kubernetes cluster - Sysdig
description: Monitorização do cluster Kubernetes no Serviço de Contentores Azure usando a Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371104"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(DEPRECIADO) Monitorize um cluster de Kubernetes do Serviço de Contentores Azure usando sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough pressupõe que [criou um cluster Kubernetes usando](container-service-kubernetes-walkthrough.md)o Serviço de Contentores Azure .

Também assume que tem as ferramentas azure cli e kubectl instaladas.

Pode testar se tiver a ferramenta `az` instalada executando:

```azurecli
az --version
```

Se não tiver a ferramenta `az` instalada, existem instruções [aqui.](https://github.com/azure/azure-cli#installation)

Pode testar se tiver a ferramenta `kubectl` instalada executando:

```console
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
A Sysdig é uma empresa de monitorização externa que pode monitorizar contentores no seu cluster Kubernetes em funcionamento em Azure. Usar a Sysdig requer uma conta sysdig ativa.
Pode inscrever-se para uma conta no [site](https://app.sysdigcloud.com)deles.

Após ter iniciado sessão no site na nuvem do Sysdig, clique no seu nome de utilizador e deverá ver a sua "Chave de Acesso" na página. 

![Chave de API do Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalação dos agentes Sysdig na Kubernetes
Para monitorizar os seus contentores, a Sysdig executa um processo em cada máquina utilizando um `DaemonSet`Kubernetes .
DaemonSets são objetos Kubernetes API que executam uma única instância de um recipiente por máquina.
São perfeitos para instalar ferramentas como o agente de monitorização do Sysdig.

Para instalar o daemonset Sysdig, deve primeiro descarregar [o modelo](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) a partir de sysdig. Guarde o ficheiro como `sysdig-daemonset.yaml`.

Em Linux e OS X pode correr:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

No PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Em seguida, edite esse ficheiro para inserir a sua Chave de Acesso, que obteve na sua conta Sysdig.

Finalmente, crie o DaemonSet:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Veja a sua monitorização
Uma vez instalados e em funcionamento, os agentes devem bombear os dados de volta para a Sysdig.  Volte para o painel de instrumentos de [sysdig](https://app.sysdigcloud.com) e deverá ver informações sobre os seus contentores.

Também pode instalar dashboards específicos da Kubernetes através do [novo assistente](https://app.sysdigcloud.com/#/dashboards/new)do dashboard .
