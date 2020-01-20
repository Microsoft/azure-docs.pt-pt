---
title: PRETERIDO Monitorar o cluster kubernetes do Azure com o Datadog
description: Monitorando o cluster kubernetes no serviço de contêiner do Azure usando o Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c8ed146a224ec4225a7a0e85c76227fb1dc71b0b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271057"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>PRETERIDO Monitorar um cluster do serviço de contêiner do Azure com o DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial pressupõe que você [criou um cluster kubernetes usando o serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Ele também pressupõe que você tenha as ferramentas `az` CLI do Azure e `kubectl` instaladas.

Você pode testar se tem a ferramenta de `az` instalada executando:

```console
$ az --version
```

Se você não tiver a ferramenta de `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).

Você pode testar se tem a ferramenta de `kubectl` instalada executando:

```console
$ kubectl version
```

Se você não tiver `kubectl` instalado, poderá executar:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog é um serviço de monitoramento que coleta dados de monitoramento de seus contêineres no cluster do serviço de contêiner do Azure. O Datadog tem um painel de integração do Docker onde você pode ver métricas específicas em seus contêineres. As métricas coletadas de seus contêineres são organizadas por CPU, memória, rede e e/s. O Datadog divide as métricas em contêineres e imagens.

Primeiro, você precisa [criar uma conta](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalando o agente Datadog com um Daemonset
DaemonSets são usados pelo kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento.

Depois de fazer logon no Datadog, você pode seguir as [instruções de Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar os agentes do Datadog no cluster usando um daemonset.

## <a name="conclusion"></a>Conclusão
Já está! Depois que os agentes estiverem ativos e em execução, você deverá ver os dados no console em alguns minutos. Você pode visitar o painel integrado do [kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) para ver um resumo do seu cluster.
