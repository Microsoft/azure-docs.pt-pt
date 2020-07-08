---
title: (PRECADO) Monitor Azure Kubernetes cluster com Datadog
description: Monitorização do cluster Kubernetes no Serviço de Contentores Azure usando Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371176"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(PRECADO) Monitorize um cluster de serviço de contentores Azure com DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough assume que [criou um cluster Kubernetes usando o Serviço de Contentores Azure.](container-service-kubernetes-walkthrough.md)

Também assume que tem o `az` Cli Azure e `kubectl` as ferramentas instaladas.

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

## <a name="datadog"></a>DataDog
O Datadog é um serviço de monitorização que recolhe dados de monitorização dos seus contentores dentro do seu cluster de Serviço de Contentores Azure. O Datadog tem um Painel de Integração docker onde pode ver métricas específicas dentro dos seus contentores. As métricas recolhidas dos seus contentores são organizadas pela CPU, Memória, Rede e E/S. Datadog divide métricas em recipientes e imagens.

Primeiro precisa [de criar uma conta](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalação do Agente Datadog com um DaemonSet
Os DaemonSets são utilizados pela Kubernetes para executar uma única instância de um recipiente em cada hospedeiro do cluster.
São perfeitos para agentes de monitorização.

Uma vez iniciado sessão no Datadog, pode seguir as instruções do [Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar agentes datadog no seu cluster utilizando um DaemonSet.

## <a name="conclusion"></a>Conclusão
Já está! Uma vez que os agentes estejam a funcionar, deverá ver dados na consola em poucos minutos. Pode visitar o painel de [kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) integrado para ver um resumo do seu cluster.
