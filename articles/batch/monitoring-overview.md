---
title: Monitor Azure Batch
description: Saiba mais sobre os serviços de monitorização do Azure, métricas, registos de diagnóstico e outras funcionalidades de monitorização do Azure Batch.
ms.topic: how-to
ms.date: 04/05/2018
ms.openlocfilehash: 9387d4894dbf06445fd43722353ca7ae98607359
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86144943"
---
# <a name="monitor-batch-solutions"></a>Monitorizar soluções do Batch

O Azure e o serviço Batch fornecem uma gama de serviços, ferramentas e APIs para monitorizar as suas soluções batch. Este artigo de visão geral ajuda-o a escolher uma abordagem de monitorização que se adapte às suas necessidades.

Para uma visão geral dos componentes e serviços Azure disponíveis para monitorizar os recursos do Azure, consulte [aplicações e recursos do Azure de Monitorização.](../azure-monitor/overview.md)

## <a name="subscription-level-monitoring"></a>Monitorização ao nível da subscrição

Ao nível da subscrição, que inclui contas Batch, o registo de atividades do [Azure](../azure-monitor/platform/platform-logs-overview.md) recolhe dados operacionais de eventos em [várias categorias](../azure-monitor/platform/activity-log.md#view-the-activity-log).

Especificamente para as contas do Batch, o registo de atividades recolhe eventos relacionados com a criação de conta e eliminação e gestão de chaves.

Uma forma de recuperar eventos do seu registo de atividades é usar o portal Azure. Clique **em Todos os serviços**Registo de  >  **Atividades**. Ou, consulta para eventos usando o Azure CLI, powerShell cmdlets, ou o AZure Monitor REST API. Também pode exportar o registo de atividades ou configurar [alertas de registo de atividades](../azure-monitor/platform/alerts-activity-log.md).

## <a name="batch-account-level-monitoring"></a>Monitorização do nível da conta do lote

Monitorize cada conta de Lote utilizando as características do [Azure Monitor](../azure-monitor/overview.md). O Azure Monitor recolhe [métricas](../azure-monitor/platform/data-platform-metrics.md) e registos de diagnóstico opcional para recursos [abrangidos](../azure-monitor/platform/platform-logs-overview.md) ao nível de uma conta Batch, tais como piscinas, empregos e tarefas. Recolher e consumir estes dados manualmente ou programáticamente para monitorizar as atividades na sua conta Batch e diagnosticar problemas. Para mais detalhes, consulte [métricas, alertas e registos do Lote para avaliação e monitorização de diagnósticos.](batch-diagnostics.md)
 
> [!NOTE]
> As métricas estão disponíveis por padrão na sua conta Batch sem configuração adicional, e têm um histórico de 30 dias de rolamento. Tem de ativar o registo de diagnóstico de uma conta Batch e poderá incorrer em custos adicionais para armazenar ou processar dados de registo de diagnóstico. 

## <a name="batch-resource-monitoring"></a>Monitorização de recursos de lote

Nas suas aplicações Batch, utilize as APIs do Lote para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas. Por exemplo:

* [Contar tarefas e nómada computacional por estado](batch-get-resource-counts.md)
* [Criar consultas para listar os recursos do Batch de forma eficiente](batch-efficient-list-queries.md)
* [Criar dependências de tarefas](batch-task-dependencies.md)
* Use uma [tarefa de gestor de emprego](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorizar o [estado de tarefa](/rest/api/batchservice/task/list#taskstate)
* Monitorize o [estado do nó](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorize o estado da [piscina](/rest/api/batchservice/pool/get#poolstate)
* Monitorize [o uso da piscina na conta](/rest/api/batchservice/pool/listusagemetrics)
* [Conde os nó de piscina por estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contadores de desempenho VM e monitorização de aplicações

* [Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço Azure que pode utilizar para monitorizar programaticamente a disponibilidade, desempenho e utilização dos seus trabalhos e tarefas do Batch. Obtenha facilmente contadores de desempenho de nós computacional (VMs) e informações personalizadas para tarefas fora dos VMs. 

  Por exemplo, consulte [Monitor e depure uma aplicação Batch .NET com Insights de Aplicação](monitor-application-insights.md) e a amostra de [código](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)que o acompanha.

  > [!NOTE]
  > Pode incorrer em custos adicionais para utilizar a Application Insights. Consulte as [opções de preços.](https://azure.microsoft.com/pricing/details/application-insights/) 
  >

* [O Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta cliente independente e independente, com destaque para ajudar a criar, depurar e monitorizar as aplicações do Azure Batch. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Configurar opcionalmente a sua solução Batch para [exibir dados de Insights de Aplicação,](https://github.com/Azure/batch-insights) tais como contadores de desempenho VM no Batch Explorer.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Saiba mais sobre [o registo de diagnóstico](batch-diagnostics.md) com o Batch.
