---
title: Recolher e analisar registos de atividades do Azure no Log Analytics | Documentos da Microsoft
description: Pode utilizar a solução de registos de atividades do Azure para analisar e pesquisar o registo de atividades do Azure em todas as suas subscrições do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: cb246ad927669ac9b8a3981134142ad9be27c6ae
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889536"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics"></a>Recolher e analisar registos de atividades do Azure no Log Analytics

![Símbolo de registos de atividades do Azure](./media/collect-activity-logs/activity-log-analytics.png)

A solução Log Analytics da atividade ajuda a analisar e pesquisar os [registo de atividades do Azure](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) todas as subscrições do Azure. O registo de atividades do Azure é um registo que disponibiliza informações aprofundadas sobre as operações executadas em recursos nas suas subscrições. O registo de atividades anteriormente era conhecido como *registos de auditoria* ou *registos operacionais* , uma vez que comunica eventos para as suas subscrições.

Utilizar o registo de atividades, pode determinar a *o que*, *quem*, e *quando* para quaisquer operações (PUT, POST, DELETE) efetuadas para os recursos na sua subscrição de escrita. Também é possível compreender o estado de operações e outras propriedades relevantes. O registo de Atividades não incluem operações de leitura (GET) nem operações para recursos que utilizam o modelo de implementação clássica.

Ao ligar os seus registos de atividades do Azure para o Log Analytics, pode:

- Analisar os registos de atividades com exibições predefinidas
- Analisar e registos de pesquisa e atividade de várias subscrições do Azure
- Manter os registos de atividade durante mais de 90 dias<sup>1</sup>
- Correlacionar os registos de atividades com o outro Azure dados de plataforma e da aplicação
- Ver as atividades operacionais agregadas por Estado
- Ver as tendências das atividades acontecendo em cada um dos seus serviços do Azure
- Relatório sobre as alterações de autorização em todos os seus recursos do Azure
- Identificar problemas de estado de funcionamento de falha ou um serviço que afetem seus recursos
- Pesquisa de registos de utilização para correlacionar as atividades do utilizador, as operações de dimensionamento automático, as alterações de autorização e service health para outros logs ou métricas do seu ambiente

<sup>1</sup>por predefinição, do Log Analytics mantém os seus registos de atividades do Azure durante 90 dias, mesmo que esteja no escalão gratuito. Em alternativa, se tiver uma definição de retenção de área de trabalho de menos de 90 dias. Se a sua área de trabalho tem retenção com mais de 90 dias, os registos de Atividades são mantidos com base no período de retenção da sua área de trabalho.

O log Analytics recolhe registos de atividade sem encargos e armazena os registos durante 90 dias sem encargos. Se armazenar os registos durante mais de 90 dias, incorre em custos de retenção de dados para os dados armazenados há mais de 90 dias.

Quando estiver no escalão de preço gratuito, registos de atividades que não se aplicam ao seu consumo de dados diário.

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria das outras soluções do Log Analytics, os dados não são recolhidos para registos de atividades por agentes. Todos os dados utilizados pela solução é fornecido diretamente a partir do Azure.

| Origem Ligada | Suportadas | Descrição |
| --- | --- | --- |
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Não | A solução não recolhe informações de agentes do Windows. |
| [Agentes do Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) | Não | A solução não recolhe informações de agentes do Linux. |
| [Grupo de gestão do SCOM](../../azure-monitor/platform/om-agents.md) | Não | A solução não recolhe informações de agentes num grupo de gestão ligado do SCOM. |
| [Conta de armazenamento do Azure](collect-azure-metrics-logs.md) | Não | A solução não recolhe informações do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder às informações de registo de atividades do Azure, tem de ter uma subscrição do Azure.

## <a name="configuration"></a>Configuração

Execute os seguintes passos para configurar a solução Log Analytics da atividade para as áreas de trabalho.

1. Ative a solução Análise do Registo de Atividades do [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) ou com o processo descrito em [Adicionar soluções do Log Analytics da Galeria de Soluções](../../azure-monitor/insights/solutions.md).
2. Configure registos de atividades para aceder à sua área de trabalho do Log Analytics.
    1. No portal do Azure, selecione a sua área de trabalho e, em seguida, clique em **registo de atividades do Azure**.
    2. Para cada subscrição, clique no nome da subscrição.  
        ![Adicionar subscrição](./media/collect-activity-logs/add-subscription.png)
    3. Na *SubscriptionName* painel, clique em **Connect**.  
        ![Ligar a subscrição](./media/collect-activity-logs/subscription-connect.png)

Iniciar sessão no portal do Azure para ligar uma subscrição do Azure à sua área de trabalho.  

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução Log Analytics da atividade à sua área de trabalho, o **registos de atividades do Azure** mosaico é adicionado ao seu dashboard de descrição geral. Este mosaico apresenta uma contagem do número de registos de atividades do Azure para as subscrições do Azure que a solução tem acesso.

![Mosaico de registos de atividades do Azure](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Registos de atividades do Azure de modo de exibição

Clique nas **registos de atividades do Azure** mosaico para abrir o **registos de atividades do Azure** dashboard. O dashboard inclui os painéis na tabela seguinte. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo** na parte inferior do painel ou ao clicar no cabeçalho do painel.

Dados de registo de atividade só é apresentado *depois de* que configurou os seus registos de atividade para ir para a solução, então, não é possível ver os dados antes dessa data.

| Painel | Descrição |
| --- | --- |
| Entradas de registo de atividades do Azure | Mostra um gráfico de barras da parte superior entrada de registo de atividades do Azure totais de registo para o intervalo de datas selecionado e mostra uma lista dos chamadores de 10 atividade principais. Clique para executar uma pesquisa de registos para o gráfico de barras <code>AzureActivity</code>. Clique num item de autor da chamada para executar uma pesquisa de registos que devolva todas as entradas de registo de atividade para esse item. |
| Registos de atividades por Estado | Mostra um gráfico de anel para obter o estado de registo de atividades do Azure para o intervalo de datas selecionado. Também mostra uma lista uma lista de registos de estado de dez principais. Clique no gráfico para executar uma pesquisa de registos para <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Clique num item de estado para executar uma pesquisa de registos que devolva todas as entradas de registo de atividade para esse registo de estado. |
| Registos de atividades por recurso | Mostra o número total de recursos com registos de atividade e apresenta uma lista de topo contagens de dez recursos com o registo para cada recurso. Clique na área total para executar uma pesquisa de registos para <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, que mostra todos os recursos do Azure disponíveis para a solução. Clique num recurso para executar uma pesquisa de registos que devolva todos os registos de atividade para esse recurso. |
| Registos de atividade pelo fornecedor de recursos | Mostra o número total de fornecedores de recursos que produzem atividade registra e lista as dez principais. Clique na área total para executar uma pesquisa de registos para <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, que mostra todos os fornecedores de recursos do Azure. Clique num fornecedor de recursos para executar uma pesquisa de registos a devolver todos os registos de atividade para o fornecedor. |

![Dashboard de registos de atividades do Azure](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>Passos Seguintes

- Criar uma [alerta](../../monitoring-and-diagnostics/alert-metric.md) quando ocorre uma atividade específica.
- Uso [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md) para ver informações detalhadas de registos de atividades.
