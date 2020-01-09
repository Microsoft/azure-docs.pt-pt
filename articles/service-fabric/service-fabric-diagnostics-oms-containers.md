---
title: Monitorar contêineres com logs de Azure Monitor
description: Use logs de Azure Monitor para monitorar contêineres em execução em clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614439"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorar contêineres com logs de Azure Monitor
 
Este artigo aborda as etapas necessárias para configurar a solução de monitoramento de contêiner de logs de Azure Monitor para exibir eventos de contêiner. Para configurar o cluster para coletar eventos de contêiner, consulte este [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitoramento de contêiner

> [!NOTE]
> Você precisa ter Azure Monitor logs configurados para o cluster, bem como ter o agente de Log Analytics implantado em seus nós. Se não estiver, siga as etapas em [Configurar Logs de Azure monitor](service-fabric-diagnostics-oms-setup.md) e [adicione o agente de log Analytics a um cluster](service-fabric-diagnostics-oms-agent.md) primeiro.

1. Depois que o cluster estiver configurado com os logs de Azure Monitor e o agente de Log Analytics, implante seus contêineres. Aguarde até que os contêineres sejam implantados antes de passar para a próxima etapa.

2. No Azure Marketplace, pesquise *solução de monitoramento de contêiner* e clique no recurso de solução de monitoramento de **contêiner** que aparece na categoria monitoramento + gerenciamento.

    ![Adicionar a solução de Contentores](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo espaço de trabalho que já foi criado para o cluster. Essa alteração dispara automaticamente o agente para iniciar a coleta de dados do Docker nos contêineres. Em cerca de 15 minutos ou mais, você deve ver a solução se acender com estatísticas e logs de entrada, conforme mostrado na imagem abaixo.

    ![Painel de Log Analytics básica](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite a coleta de vários logs específicos de contêiner que podem ser consultados em logs de Azure Monitor ou usados para visualizar indicadores de desempenho. Os tipos de log que são coletados são:

* ContainerInventory: mostra informações sobre o local, o nome e as imagens do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, incluindo IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs do Docker (stdout, etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do Docker que foram executados
* Desempenho: contadores de desempenho, incluindo CPU de contêiner, memória, tráfego de rede, e/s de disco e métricas personalizadas dos computadores host



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [solução de contêineres de logs de Azure monitor](../azure-monitor/insights/containers.md).
* Leia mais sobre orquestração de contêiner em Service Fabric- [Service Fabric e contêineres](service-fabric-containers-overview.md)
* Familiarize-se com os recursos de [pesquisa de logs e consulta](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs de Azure monitor
* Configurar logs de Azure Monitor para configurar regras de [alerta automatizado](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico