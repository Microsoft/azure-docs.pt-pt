---
title: (PRETERIDO) Monitorizar o cluster de Kubernetes do Azure com o Datadog
description: Monitorização de cluster do Kubernetes no Azure Container Service com o Datadog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6a682c199b40035bfd44fc5611a7d44b49f7b3ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60712343"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(PRETERIDO) Monitorizar um cluster do Azure Container Service com o DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo pressupõe que tenha [criado um cluster de Kubernetes com o Azure Container Service](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem o `az` cli do Azure e `kubectl` ferramentas instaladas.

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

## <a name="datadog"></a>DataDog
O Datadog é um serviço de monitoramento que recolhe dados de monitorização de seus contentores no seu cluster do Azure Container Service. O Datadog tem um Dashboard de integração de Docker onde pode ver métricas específicas dentro de seus contentores. As métricas reunidas a partir de seus contentores são organizadas por CPU, memória, rede e e/s. O Datadog divide as métricas em contentores e imagens.

Precisa primeiro [criar uma conta](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalar o agente de Datadog com um DaemonSet
DaemonSets são utilizados pelo Kubernetes para executar uma única instância de um contentor em cada anfitrião no cluster.
Eles são perfeitos para a execução de agentes de monitorização.

Assim que tiver sessão iniciada para o Datadog, pode seguir a [Datadog instruções](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar agentes de Datadog em seu cluster com um DaemonSet.

## <a name="conclusion"></a>Conclusão
Já está! Assim que os agentes estão em execução, verá dados na consola dentro de alguns minutos. Pode visitar o integrada [dashboard do kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) para ver um resumo do cluster.
