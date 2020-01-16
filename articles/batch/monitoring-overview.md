---
title: Monitorar o lote do Azure | Microsoft Docs
description: Saiba mais sobre os serviços de monitoramento do Azure, as métricas, os logs de diagnóstico e outros recursos de monitoramento para o lote do Azure.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: jushiman
ms.openlocfilehash: 05881dc5c2df9103a57af699cf807d6d66db27b9
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029532"
---
# <a name="monitor-batch-solutions"></a>Monitorizar soluções do Batch

O Azure e o serviço de lote fornecem uma variedade de serviços, ferramentas e APIs para monitorar suas soluções de lote. Este artigo de visão geral ajuda a escolher uma abordagem de monitoramento que atenda às suas necessidades.

Para obter uma visão geral dos componentes e serviços do Azure disponíveis para monitorar os recursos do Azure, consulte [monitoramento de aplicativos e recursos do](../monitoring-and-diagnostics/monitoring-overview.md)Azure.

## <a name="subscription-level-monitoring"></a>Monitoramento em nível de assinatura

No nível da assinatura, que inclui contas do lote, o [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md) coleta dados de evento operacional em [várias categorias](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log).

Para contas do lote especificamente, o log de atividades coleta eventos relacionados à criação e exclusão de contas e gerenciamento de chaves.

Uma maneira de recuperar eventos de seu log de atividades é usar o portal do Azure. Clique em **todos os serviços** > **log de atividades**. Ou, consulte eventos usando o CLI do Azure, cmdlets do PowerShell ou a API REST do Azure Monitor. Você também pode exportar o log de atividades ou configurar [alertas do log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitoramento em nível de conta do lote

Monitore cada conta do lote usando os recursos do [Azure monitor](../azure-monitor/overview.md). Azure Monitor coleta [métricas](../azure-monitor/platform/data-platform-metrics.md) e, opcionalmente, [logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) para recursos com escopo no nível de uma conta do lote, como pools, trabalhos e tarefas. Colete e consuma esses dados manualmente ou programaticamente para monitorar atividades em sua conta do lote e para diagnosticar problemas. Para obter detalhes, consulte [métricas do lote, alertas e logs para avaliação e monitoramento de diagnóstico](batch-diagnostics.md).
 
> [!NOTE]
> As métricas estão disponíveis por padrão em sua conta do lote sem configuração adicional e têm um histórico contínuo de 30 dias. Você deve habilitar o log de diagnóstico para uma conta do lote e pode incorrer em custos adicionais para armazenar ou processar dados de log de diagnóstico. 

## <a name="batch-resource-monitoring"></a>Monitoramento de recursos do lote

Em seus aplicativos do lote, use as APIs do lote para monitorar ou consultar o status de seus recursos, incluindo trabalhos, tarefas, nós e pools. Por exemplo:

* [Contar tarefas e nós de computação por Estado](batch-get-resource-counts.md)
* [Criar consultas para listar recursos do lote com eficiência](batch-efficient-list-queries.md)
* [Criar dependências de tarefa](batch-task-dependencies.md)
* Usar uma [tarefa do Gerenciador de trabalho](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorar o [estado da tarefa](/rest/api/batchservice/task/list#taskstate)
* Monitorar o [estado do nó](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorar o [estado do pool](/rest/api/batchservice/pool/get#poolstate)
* Monitorar [o uso do pool na conta](/rest/api/batchservice/pool/listusagemetrics)
* [Contar nós de pool por Estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contadores de desempenho de VM e monitoramento de aplicativos

* [Application insights](../azure-monitor/app/app-insights-overview.md) é um serviço do Azure que você pode usar para monitorar de forma programática a disponibilidade, o desempenho e o uso de trabalhos e tarefas do lote. Obtenha facilmente contadores de desempenho de nós de computação (VMs) e informações personalizadas para tarefas fora das VMs. 

  Para obter um exemplo, consulte [monitorar e depurar um aplicativo .net do lote com Application insights](monitor-application-insights.md) e o [exemplo de código](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)fornecido.

  > [!NOTE]
  > Você pode incorrer em custos adicionais para usar Application Insights. Consulte as [Opções de preços](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta de cliente autônoma, gratuita e com recursos avançados para ajudar a criar, depurar e monitorar aplicativos do lote do Azure. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Opcionalmente, configure sua solução do lote para [exibir dados de Application insights](https://github.com/Azure/batch-insights) , como contadores de desempenho de VM no batch Explorer.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Saiba mais sobre o [log de diagnóstico](batch-diagnostics.md) com o lote.