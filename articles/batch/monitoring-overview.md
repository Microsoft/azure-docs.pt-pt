---
title: Monitorizar o Azure Batch | Documentos da Microsoft
description: Saiba mais sobre os serviços de monitorização do Azure, métricas, registos de diagnóstico e outras funcionalidades de monitorização para o Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: b0243b37f725fc977337b72998d610e9bda71a86
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128860"
---
# <a name="monitor-batch-solutions"></a>Monitorizar soluções do Batch

Azure e o serviço Batch fornecem uma variedade de serviços, ferramentas e APIs para monitorizar as soluções do Batch. Este artigo de descrição geral ajuda a escolher uma abordagem de monitorização adequada às suas necessidades.

Para uma descrição geral dos componentes do Azure e serviços disponíveis para monitorizar os recursos do Azure, consulte [aplicações de monitorização do Azure e recursos](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitorização de nível de assinatura

Ao nível da subscrição, incluindo as contas do Batch, o [registo de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md) recolhe dados de eventos operacionais [várias categorias](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

Especificamente, para contas do Batch no registo de atividades recolhe os eventos relacionados com a gestão de criação e eliminação e a chave de conta.

Uma forma de obter eventos a partir do seu registo de atividades é utilizar o portal do Azure. Clique em **todos os serviços** > **registo de atividades**. Em alternativa, consulta para eventos com a CLI do Azure, cmdlets do PowerShell ou a API de REST do Azure Monitor. Também pode exportar o registo de atividades, ou configurar [alertas de registo de atividade](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitorização de nível de conta do batch

Monitorizar cada conta do Batch utilizar funcionalidades do [do Azure Monitor](../azure-monitor/overview.md). Monitor do Azure recolhe [métricas](../azure-monitor/platform/data-platform-metrics.md) e, opcionalmente [registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) para recursos no âmbito no nível de uma conta do Batch, como conjuntos, trabalhos e tarefas. Recolher e consumir estes dados manualmente ou através de programação para monitorizar atividades na sua conta do Batch e para diagnosticar problemas. Para obter detalhes, consulte [métricas, alertas e registos para a avaliação de diagnóstico e monitorização do Batch](batch-diagnostics.md).
 
> [!NOTE]
> As métricas estão disponíveis por predefinição na sua conta do Batch sem configuração adicional e têm um histórico de sem interrupção de 30 dias. Tem de ativar o registo de diagnósticos para uma conta do Batch e pode implicar custos adicionais para armazenar ou processar os dados de registo de diagnóstico. 

## <a name="batch-resource-monitoring"></a>Monitorização de recursos do batch

As aplicações do Batch, utilize as APIs do Batch para monitorizar ou consultar o estado dos seus recursos, incluindo trabalhos, tarefas, nós e conjuntos. Por exemplo:

* [Contagem de tarefas e nós pelo Estado de computação](batch-get-resource-counts.md)
* [Criar consultas para listar recursos do Batch de forma eficiente](batch-efficient-list-queries.md)
* [Criar as dependências de tarefas](batch-task-dependencies.md)
* Utilize um [tarefa de gestão](/rest/api/batchservice/job/add#jobmanagertask)
* Monitor de [estado de tarefas](/rest/api/batchservice/task/list#taskstate)
* Monitor de [estado do nó](/rest/api/batchservice/computenode/list#computenodestate)
* Monitor de [estado do agrupamento](/rest/api/batchservice/pool/get#poolstate)
* Monitor [utilização na conta do agrupamento](/rest/api/batchservice/pool/listusagemetrics)
* [Nós do conjunto de contagem por Estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contadores de desempenho da VM e monitorização de aplicações

* [O Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço do Azure, pode utilizar para monitorizar programaticamente a disponibilidade, desempenho e utilização dos seus trabalhos de lote e tarefas. Facilmente contadores de desempenho do get de nós de computação (VMs) e informações personalizadas para tarefas das VMs. 

  Por exemplo, veja [Monitor e depurar uma aplicação .NET do Batch com o Application Insights](monitor-application-insights.md) e que o acompanha [exemplo de código](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Pode implicar custos adicionais para utilizar o Application Insights. Consulte a [as opções de preços](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Explorador do batch](https://github.com/Azure/BatchExplorer) é uma ferramenta de cliente autónoma gratuita e rica em funcionalidades, para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Opcionalmente, configure a sua solução do Batch para [exibir dados do Application Insights](https://github.com/Azure/batch-insights) como contadores de desempenho da VM no Explorador do Batch.


## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Saiba mais sobre [registo de diagnósticos](batch-diagnostics.md) com o Batch.