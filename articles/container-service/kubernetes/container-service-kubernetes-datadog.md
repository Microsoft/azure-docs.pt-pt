---
title: (DEPRECIADO) Monitor Azure Kubernetes cluster com Datadog
description: Monitorização do cluster Kubernetes no Serviço de Contentores Azure usando Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371176"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(DEPRECIADO) Monitorize um cluster de serviço de contentores Azure com DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough pressupõe que [criou um cluster Kubernetes usando](container-service-kubernetes-walkthrough.md)o Serviço de Contentores Azure .

Também assume que tem `az` o Azure `kubectl` cli e ferramentas instaladas.

Pode testar se tiver `az` a ferramenta instalada executando:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui.](https://github.com/azure/azure-cli#installation)

Pode testar se tiver `kubectl` a ferramenta instalada executando:

```console
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog é um serviço de monitorização que recolhe dados de monitorização dos seus contentores dentro do seu cluster de Serviço de Contentores Azure. Datadog tem um Dashboard de Integração de Docker onde você pode ver métricas específicas dentro dos seus recipientes. As métricas recolhidas dos seus contentores são organizadas pela CPU, Memory, Network e I/O. Datadog divide métricas em recipientes e imagens.

Primeiro precisa [criar uma conta](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalação do Agente Datadog com um DaemonSet
DaemonSets são usados por Kubernetes para executar uma única instância de um recipiente em cada hospedeiro no cluster.
São perfeitos para agentes de monitorização.

Depois de ter iniciado o sessão no Datadog, pode seguir as [instruções do Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar agentes Datadog no seu cluster utilizando um DaemonSet.

## <a name="conclusion"></a>Conclusão
Já está! Uma vez que os agentes estejam a funcionar, deverá ver os dados na consola em poucos minutos. Pode visitar o painel integrado de [kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) para ver um resumo do seu cluster.
