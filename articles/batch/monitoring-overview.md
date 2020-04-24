---
title: Lote De Monitor Azure
description: Conheça os serviços de monitorização do Azure, métricas, registos de diagnóstico e outras funcionalidades de monitorização para o Lote Azure.
ms.topic: article
ms.date: 04/05/2018
ms.openlocfilehash: c8b11d0b7187d6c4b5b3ccff1e19eb533ff3f0da
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113896"
---
# <a name="monitor-batch-solutions"></a>Monitorizar soluções do Batch

O serviço Azure e o Batch fornecem uma gama de serviços, ferramentas e APIs para monitorizar as suas soluções De Lote. Este artigo de visão geral ajuda-o a escolher uma abordagem de monitorização que se adapte às suas necessidades.

Para uma visão geral dos componentes e serviços do Azure disponíveis para monitorizar os recursos do Azure, consulte as aplicações e recursos do Azure de [monitorização.](../monitoring-and-diagnostics/monitoring-overview.md)

## <a name="subscription-level-monitoring"></a>Monitorização ao nível da subscrição

Ao nível da subscrição, que inclui contas de Lote, o registo de [atividade sinuoso](../azure-monitor/platform/platform-logs-overview.md) do Azure recolhe dados de eventos operacionais em [várias categorias.](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log)

Para as contas do Lote especificamente, o registo de atividade recolhe eventos relacionados com a criação de contas e a eliminação e gestão chave.

Uma forma de recuperar eventos do registo de atividade é usar o portal Azure. Clique em **Todos os serviços** > **Registo de Atividades**. Ou, consulta para eventos que utilizem os cmdlets Azure CLI, PowerShell ou a API REST Do Monitor Azure. Também pode exportar o registo de atividade, ou configurar alertas de [registo de atividade.](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md)

## <a name="batch-account-level-monitoring"></a>Monitorização ao nível da conta do lote

Monitorize cada conta de Lote utilizando características do [Monitor Azure](../azure-monitor/overview.md). O Azure Monitor recolhe [métricas](../azure-monitor/platform/data-platform-metrics.md) e [registos](../azure-monitor/platform/platform-logs-overview.md) de diagnóstico opcional para recursos mapeados ao nível de uma conta de Lote, tais como piscinas, empregos e tarefas. Recolha e consuma estes dados manualmente ou programáticamente para monitorizar as atividades na sua conta Do Lote e diagnosticar problemas. Para mais detalhes, consulte [as métricas do lote, alertas e registos para avaliação e monitorização de diagnóstico](batch-diagnostics.md).
 
> [!NOTE]
> As métricas estão disponíveis por padrão na sua conta Batch sem configuração adicional, e têm um histórico de 30 dias. Tem de ativar o registo de diagnóstico para uma conta Batch e pode incorrer em custos adicionais para armazenar ou processar dados de registo de diagnóstico. 

## <a name="batch-resource-monitoring"></a>Monitorização de recursos de lote

Nas suas aplicações de Lote, utilize as APIs do Lote para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas. Por exemplo:

* [Contar tarefas e calcular os nódosos por estado](batch-get-resource-counts.md)
* [Criar consultas para listar os recursos do Lote de forma eficiente](batch-efficient-list-queries.md)
* [Criar dependências de tarefas](batch-task-dependencies.md)
* Use uma tarefa de gestor de [emprego](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorizar o [estado de tarefa](/rest/api/batchservice/task/list#taskstate)
* Monitorize o estado do [nó](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorize o estado da [piscina](/rest/api/batchservice/pool/get#poolstate)
* Monitorizar [o uso da piscina na conta](/rest/api/batchservice/pool/listusagemetrics)
* [Contagem de nomes de piscina por estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contadores de desempenho VM e monitorização de aplicações

* [Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço Azure que pode utilizar para monitorizar programáticamente a disponibilidade, desempenho e utilização dos seus trabalhos e tarefas do Lote. Obtenha facilmente contadores de desempenho a partir de nós de computação (VMs) e informações personalizadas para tarefas fora dos VMs. 

  Por exemplo, consulte [monitor e depura uma aplicação Batch .NET com Insights](monitor-application-insights.md) de Aplicação e a amostra de [código](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)que o acompanha .

  > [!NOTE]
  > Pode incorrer em custos adicionais para utilizar os Insights da Aplicação. Consulte as [opções de preços](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [O Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta de cliente gratuita, rica e autónoma para ajudar a criar, depurar e monitorizar aplicações do Lote Azure. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Configure opcionalmente a sua solução de Lote para exibir dados de Insights de [Aplicação,](https://github.com/Azure/batch-insights) como contadores de desempenho VM no Batch Explorer.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Saiba mais sobre [o diagnóstico de login](batch-diagnostics.md) com Batch.