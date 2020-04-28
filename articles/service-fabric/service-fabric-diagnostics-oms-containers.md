---
title: Monitorize contentores com troncos do Monitor Azure
description: Utilize registos do Monitor Azure para monitorizar os recipientes em funcionamento em clusters de tecido de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614439"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorize contentores com troncos do Monitor Azure
 
Este artigo cobre as etapas necessárias para a instalação da solução de monitorização do contentor de registos Azure Monitor para ver os eventos dos contentores. Para configurar o seu cluster para recolher eventos de contentores, consulte este [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitorização do recipiente

> [!NOTE]
> É necessário instalar os registos do Monitor Azure para o seu cluster, bem como ter o agente Log Analytics implantado nos seus nós. Se não o fizer, siga os passos em [Registos do Monitor Azure](service-fabric-diagnostics-oms-setup.md) e [adicione primeiro o agente Log Analytics a um cluster.](service-fabric-diagnostics-oms-agent.md)

1. Assim que o seu cluster for configurado com os registos do Monitor Azure e o agente Log Analytics, coloque os seus recipientes. Aguarde que os seus recipientes sejam implantados antes de passar para o próximo passo.

2. No Azure Marketplace, procure solução de monitorização de *contentores* e clique no recurso Solução de Monitorização de **Contentores** que aparece na categoria Monitoring + Management.

    ![Adicionar a solução de Contentores](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo espaço de trabalho que já foi criado para o cluster. Esta alteração aciona automaticamente o agente para começar a recolher dados de estivadores nos contentores. Em cerca de 15 minutos, deverá ver a solução iluminar-se com registos e estatísticas de entrada, como mostra a imagem abaixo.

    ![Painel de análise de log básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite a recolha de vários registos específicos do contentor que podem ser consultados em registos do Monitor Azure ou utilizados para visualizar indicadores de desempenho. Os tipos de registo recolhidos são:

* ContainerInventário: mostra informações sobre localização, nome e imagens do contentor
* ContainerImageInventory: informações sobre imagens implementadas, incluindo IDs ou tamanhos
* ContainerLog: registos de erros específicos, registos de estivadores (dstout, etc.) e outras entradas
* ContainerServiceLog: comandos da daemon do estivador que foram executados
* Perf: contadores de desempenho incluindo cpu de contentores, memória, tráfego de rede, disco i/o, e métricas personalizadas das máquinas hospedeiras



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a solução de contentores de [logs do Monitor Azure.](../azure-monitor/insights/containers.md)
* Ler mais sobre orquestração de contentores em Tecido de Serviço - [Tecido de Serviço e recipientes](service-fabric-containers-overview.md)
* Familiarize-se com as funcionalidades de [pesquisa de registos e consulta](../log-analytics/log-analytics-log-searches.md) oferecidas como parte dos registos do Monitor Azure
* Configure Registos do Monitor Azure para criar regras [de alerta automatizadas](../log-analytics/log-analytics-alerts.md) para ajudar na deteção e diagnóstico