---
title: Monitorize recipientes com registos do Monitor Azure
description: Utilize registos do Monitor Azure para monitorizar os recipientes que funcionam em aglomerados de tecidos de serviço Azure.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 6217569dc50517c88a5a8a7bc0f3752e7e327f4e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626661"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorize recipientes com registos do Monitor Azure
 
Este artigo abrange as etapas necessárias à criação da solução de monitorização dos registos do Azure Monitor para visualizar os eventos dos contentores. Para configurar o seu cluster para recolher eventos de contentores, consulte este [tutorial passo a passo.](service-fabric-tutorial-monitoring-wincontainers.md) 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitorização do contentor

> [!NOTE]
> Precisa de ter registos do Azure Monitor configurados para o seu cluster, bem como ter o agente Log Analytics implantado nos seus nós. Caso não o faça, siga primeiro os passos nos [registos do Azure Monitor](service-fabric-diagnostics-oms-setup.md) e [adicione primeiro o agente Log Analytics a um cluster.](service-fabric-diagnostics-oms-agent.md)

1. Assim que o seu cluster estiver configurado com registos do Monitor Azure e do agente Log Analytics, coloque os seus contentores. Aguarde que os seus recipientes sejam colocados antes de passar para o passo seguinte.

2. No Mercado Azure, procure a *Solução de Monitorização de Contentores* e clique no recurso **solução de monitorização** do contentor que aparece na categoria Monitoring + Management.

    ![Adicionar a solução de Contentores](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo espaço de trabalho que já foi criado para o cluster. Esta alteração aciona automaticamente o agente para começar a recolher dados de estivadores nos contentores. Em cerca de 15 minutos, deverá ver a solução iluminada com registos e estatísticas de entrada, como mostra a imagem abaixo.

    ![Painel básico de análise de registo](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite a recolha de vários registos específicos do contentor que podem ser consultados em registos do Monitor Azure ou utilizados para visualizar indicadores de desempenho. Os tipos de registo que são recolhidos são:

* ContentorInventory: mostra informações sobre localização, nome e imagens do contentor
* ContainerImageInventory: informações sobre imagens implementadas, incluindo IDs ou tamanhos
* ContainerLog: registos de erros específicos, registos de estivadores (stdout, etc.) e outras entradas
* ContainerServiceLog: comandos domão que foram executados
* Perf: contadores de desempenho, incluindo cpu de contentores, memória, tráfego de rede, disco i/o e métricas personalizadas das máquinas hospedeiras



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a solução de registos de registos Azure Monitor Containers](../azure-monitor/containers/containers.md).
* Leia mais sobre orquestração de contentores em Tecido de Serviço - [Tecido de Serviço e contentores](service-fabric-containers-overview.md)
* Familiarize-se com as funcionalidades [de pesquisa e consulta](../azure-monitor/logs/log-query-overview.md) de registo oferecidas como parte dos registos do Azure Monitor
* Configurar registos do Azure Monitor para criar regras [de alerta automatizadas](../azure-monitor/alerts/alerts-overview.md) para ajudar na deteção e diagnóstico
