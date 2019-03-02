---
title: Monitorizar contentores no Azure Service Fabric com os registos do Azure Monitor | Documentos da Microsoft
description: Utilize registos de Azure Monitor para monitorizar contentores em execução em clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d5840db718191c9b67a8b28a2efccd55146ae510
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246938"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorizar contentores com os registos do Azure Monitor
 
Este artigo aborda os passos necessários para configurar o contentor de registos do Azure Monitor, solução de monitorização para ver eventos de contentor. Para configurar o seu cluster para recolher eventos de contentor, veja este [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar o solução de monitorização do contentor

> [!NOTE]
> Tem de ter o Azure Monitor registos configurar para o seu cluster, bem como o agente de Log Analytics implementados em seus nós. Se não o fizer, siga os passos em [configurar os registos do Azure Monitor](service-fabric-diagnostics-oms-setup.md) e [adicionar o agente Log Analytics a um cluster](service-fabric-diagnostics-oms-agent.md) primeiro.

1. Assim que o seu cluster está configurado com os registos do Azure Monitor e o agente Log Analytics, implemente os contentores. Aguarde que os contentores ser implementado antes de passar para o passo seguinte.

2. No Azure Marketplace, procure *solução de monitorização de contentores* e clique nas **solução de monitorização de contentores** recurso que aparece em monitorização + gestão categoria.

    ![Adicionar a solução de Contentores](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro da mesma área de trabalho que já foi criada para o cluster. Esta alteração aciona automaticamente o agente para iniciar a recolha de dados de docker nos contentores. Em cerca de 15 minutos ou menos, deverá ver a solução leve a cópia de segurança com registos de entrada e estatísticas, conforme mostrado na imagem abaixo.

    ![Dashboard de análise de registos básicas](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite que a coleção de vários registos de contentor específicas que podem ser consultados nos registos do Azure Monitor ou usados para visualizar os indicadores de desempenho. Os tipos de registo que são recolhidos são:

* ContainerInventory: mostra informações sobre a localização do contentor, o nome e imagens
* ContainerImageInventory: informações sobre imagens implantadas, incluindo IDs ou tamanhos
* ContainerLog: registos de erro específico, registos do docker (stdout, etc.) e outras entradas
* ContainerServiceLog: comandos de daemon de docker que foram executados
* Desempenho: contadores de desempenho incluindo contentor cpu, memória, o tráfego de rede, disco e/s e métricas personalizadas dos computadores anfitrião



## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [do Azure Monitor registos de solução de contentores](../azure-monitor/insights/containers.md).
* Saiba mais sobre a orquestração de contentores no Service Fabric - [Service Fabric e contentores](service-fabric-containers-overview.md)
* Familiarizar-se com o [pesquisas e consultas de registo](../log-analytics/log-analytics-log-searches.md) funcionalidades oferecidos como parte dos registos do Azure Monitor
* Configurar registos de Monitor do Azure para configurar [alertas automáticos](../log-analytics/log-analytics-alerts.md) regras para ajudar a detetar e diagnóstico