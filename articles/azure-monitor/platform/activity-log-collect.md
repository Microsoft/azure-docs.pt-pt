---
title: Recolher e analisar registos de atividade do Azure no espaço de trabalho do Log Analytics Microsoft Docs
description: Recolher o Registo de Atividades do Azure em Registos de MonitorEs Azure e utilizar a solução de monitorização para analisar e pesquisar o registo de atividade do Azure em todas as suas subscrições do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 044f974d83eba098820639e67412110329d5ad7d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249116"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Recolher e analisar registos de atividade do Azure no espaço de trabalho do Log Analytics no Monitor Azure

> [!WARNING]
> Agora pode recolher o log da Atividade num espaço de trabalho do Log Analytics utilizando uma definição de diagnóstico semelhante à forma como recolhe registos de recursos. Consulte recolher e analisar registos de atividade do Azure no espaço de [trabalho do Log Analytics no Monitor Azure](diagnostic-settings-legacy.md).

O [Registo de Atividades do Azure](platform-logs-overview.md) fornece informações sobre eventos de nível de subscrição que ocorreram na sua subscrição Azure. Este artigo descreve como recolher o Registo de Atividades num espaço de trabalho do Log Analytics e como utilizar a solução de [monitorização](../insights/solutions.md)do Log Analytics de Atividades, que fornece consultas de registo e vistas para analisar estes dados. 

Ligar o Registo de Atividade a um espaço de trabalho de Log Analytics proporciona os seguintes benefícios:

- Consolidar o Registo de Atividades de várias subscrições do Azure num único local para análise.
- Registo de Atividades da Loja por mais de 90 dias.
- Correlacionar dados do Registo de Atividades com outros dados de monitorização recolhidos pelo Monitor Azure.
- Utilize consultas de [registo](../log-query/log-query-overview.md) para realizar análises complexas e obter informações profundas sobre as entradas de Registo de Atividade.

## <a name="connect-to-log-analytics-workspace"></a>Ligue-se ao espaço de trabalho log Analytics
Um único espaço de trabalho pode ser ligado ao Registo de Atividades para múltiplas subscrições no mesmo inquilino Azure. Para recolha em vários inquilinos, consulte Collect Azure Activity Logs num espaço de trabalho log Analytics através de [subscrições em diferentes inquilinos do Azure Ative Directory.](activity-log-collect-tenants.md)

> [!IMPORTANT]
> Poderá receber um erro com o seguinte procedimento se os fornecedores de recursos Microsoft.OperationalInsights e Microsoft.OperationsManagement não estiverem registados para a sua subscrição. Consulte [os fornecedores e tipos](../../azure-resource-manager/management/resource-providers-and-types.md) de recursos azure para registar estes fornecedores.

Utilize o seguinte procedimento para ligar o Registo de Atividade ao seu espaço de trabalho Log Analytics:

1. A partir do menu de espaços de **trabalho Log Analytics** no portal Azure, selecione o espaço de trabalho para recolher o Registo de Atividades.
1. Na secção **Fontes** de Dados do Espaço de Trabalho do menu do espaço de trabalho, selecione **o registo da Atividade Do Azure**.
1. Clique na subscrição que pretende ligar.

    ![Áreas de Trabalho](media/activity-log-export/workspaces.png)

1. Clique em Ligar o **Connect** para ligar o registo de atividade na subscrição ao espaço de trabalho selecionado. Se a subscrição já estiver ligada a outro espaço de trabalho, clique em **Desligar** primeiro para desconectá-la.

    ![Conectar espaços de trabalho](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analisar no espaço de trabalho log Analytics
Quando ligar um Registo de Atividade a um espaço de trabalho de Log Analytics, as entradas serão escritas para o espaço de trabalho numa tabela chamada **AzureActivity** que pode recuperar com uma consulta de [log](../log-query/log-query-overview.md). A estrutura deste quadro varia consoante a [categoria de entrada de registo](activity-log-view.md#categories-in-the-activity-log). Consulte o esquema do evento De Log de Atividade do [Azure](activity-log-schema.md) para obter uma descrição de cada categoria.

## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitorização de Logs de Atividade Analytics
A solução de monitorização do Azure Log Analytics inclui múltiplas consultas de registo e vistas para analisar os registos de Registo de Atividade sintetizadores no seu espaço de trabalho Log Analytics.

### <a name="install-the-solution"></a>Instalar a solução
Utilize o procedimento em [Instalar uma solução de monitorização](../insights/solutions.md#install-a-monitoring-solution) para instalar a solução **Activity Log Analytics.** Não é necessária uma configuração adicional.

### <a name="use-the-solution"></a>Utilizar a solução
As soluções de monitorização são acedidas a partir do menu **Monitor** no portal Azure. Selecione **Mais** na secção **Insights** para abrir a página **'Overview'** com os azulejos da solução. O azulejo **Azure Activity Logs** apresenta uma contagem do número de registos **azureActivity** no seu espaço de trabalho.

![Mosaico de registos de atividades do Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no azulejo **Azure Activity Logs** para abrir a vista Registos de Atividade sintetizações **do Azure.** A vista inclui as peças de visualização na tabela a seguir. Cada peça lista até 10 itens que correspondam aos critérios dessas peças para o intervalo de tempo especificado. Pode executar uma consulta de registo que devolve todos os registos correspondentes clicando **Ver tudo** na parte inferior da peça.

![Dashboard de registos de atividades do Azure](media/collect-activity-logs/activity-log-dash.png)

| Parte de visualização | Descrição |
| --- | --- |
| Entradas de registo de atividades do Azure | Mostra um gráfico de barras do top Azure Activity Log registo total total para o intervalo de data que selecionou e mostra uma lista dos 10 melhores participantes da atividade. Clique na tabela de barras para fazer uma pesquisa de registo para `AzureActivity`. Clique num item de chamada para executar uma pesquisa de registo devolvendo todas as entradas de Registo de Atividade para esse item. |
| Registos de atividades por Estado | Mostra um gráfico de donut para o estado de Registo de Atividade seletiva do Azure para a gama de datas selecionada e uma lista dos dez melhores registos de estado. Clique na tabela para fazer uma consulta de registo para `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Clique num item de estado para executar uma pesquisa de registo devolvendo todas as entradas de Registo de Atividade para esse registo de estado. |
| Registos de atividades por recurso | Mostra o número total de recursos com Registos de Atividade e lista os dez melhores recursos com contagens recorde para cada recurso. Clique na área total para fazer uma pesquisa de registo para `AzureActivity | summarize AggregatedValue = count() by Resource`, que mostra todos os recursos Do Azure disponíveis para a solução. Clique num recurso para executar uma consulta de registo devolvendo todos os registos de atividade para esse recurso. |
| Registos de atividade pelo fornecedor de recursos | Mostra o número total de fornecedores de recursos que produzem Registos de Atividade e lista o top 10. Clique na área total para fazer uma consulta de registo para `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, que mostra todos os fornecedores de recursos Azure. Clique num fornecedor de recursos para executar uma consulta de registo devolvendo todos os registos de atividade para o fornecedor. |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [Registo de Atividades](platform-logs-overview.md).
- Saiba mais sobre a plataforma de [dados Azure Monitor.](data-platform.md)
- Utilize consultas de [registo](../log-query/log-query-overview.md) para visualizar informações detalhadas do seu Registo de Atividades.
