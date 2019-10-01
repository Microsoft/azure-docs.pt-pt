---
title: Coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics | Microsoft Docs
description: Colete o log de atividades do Azure em logs de Azure Monitor e use a solução de monitoramento para analisar e pesquisar o log de atividades do Azure em todas as suas assinaturas do Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: bwren
ms.openlocfilehash: affefa96d6e38a88c994938115ddf44bcf8bd36d
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695053"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor
O [log de atividades do Azure](activity-logs-overview.md) fornece informações sobre eventos no nível da assinatura que ocorreram em sua assinatura do Azure. Este artigo descreve como coletar o log de atividades em um espaço de trabalho Log Analytics e como usar a [solução de monitoramento](../insights/solutions.md)de análise do log de atividades, que fornece consultas de log e exibições para a análise desses dados. 

Conectar o log de atividades a um espaço de trabalho Log Analytics fornece os seguintes benefícios:

- Consolide o log de atividades de várias assinaturas do Azure em um local para análise.
- Armazene entradas do log de atividades por mais de 90 dias.
- Correlacione os dados do log de atividades com outros dados de monitoramento coletados pelo Azure Monitor.
- Use [consultas de log](../log-query/log-query-overview.md) para executar uma análise complexa e obter informações aprofundadas sobre entradas do log de atividades.

## <a name="connect-to-log-analytics-workspace"></a>Conectar-se ao Log Analytics espaço de trabalho
Um log de atividades pode ser conectado a apenas um espaço de trabalho, mas um único espaço de trabalho pode ser conectado ao log de atividades para várias assinaturas no mesmo locatário do Azure. Para a coleta em vários locatários, consulte [coletar logs de atividades do Azure em um espaço de trabalho log Analytics entre assinaturas em locatários diferentes do Azure Active Directory](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Você poderá receber um erro com o procedimento a seguir se os provedores de recursos Microsoft. OperationalInsights e Microsoft. OperationsManagement não estiverem registrados para sua assinatura. Consulte [provedores de recursos do Azure e tipos](../../azure-resource-manager/resource-manager-supported-services.md) para registrar esses provedores.

Use o procedimento a seguir para conectar o log de atividades ao seu espaço de trabalho do Log Analytics:

1. No menu **log Analytics espaços de trabalho** na portal do Azure, selecione o espaço de trabalho para coletar o log de atividades.
1. Na seção **fontes de dados de espaço de trabalho** do menu do espaço de trabalho, selecione **log de atividades do Azure**.
1. Clique na assinatura que você deseja conectar.

    ![Áreas de trabalho](media/activity-log-export/workspaces.png)

1. Clique em **conectar** para conectar o log de atividades na assinatura ao espaço de trabalho selecionado. Se a assinatura já estiver conectada a outro espaço de trabalho, clique em **Desconectar** primeiro para desconectá-la.

    ![Conectar espaços de trabalho](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analisar no espaço de trabalho Log Analytics
Quando você conecta um log de atividades a um espaço de trabalho Log Analytics, as entradas são gravadas no espaço de trabalho em uma tabela chamada **AzureActivity** que você pode recuperar com uma [consulta de log](../log-query/log-query-overview.md). A estrutura dessa tabela varia dependendo da [categoria da entrada de log](activity-logs-overview.md#categories-in-the-activity-log). Consulte [esquema de eventos do log de atividades do Azure](activity-log-schema.md) para obter uma descrição de cada categoria.

## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitoramento de análise de logs de atividades
A solução de monitoramento de Log Analytics do Azure inclui várias consultas de log e exibições para analisar os registros de log de atividades em seu espaço de trabalho do Log Analytics.

### <a name="install-the-solution"></a>Instalar a solução
Use o procedimento em [instalar uma solução de monitoramento](../insights/solutions.md#install-a-monitoring-solution) para instalar a solução de **análise do log de atividades** . Não há nenhuma configuração adicional necessária.

### <a name="use-the-solution"></a>Utilizar a solução
As soluções de monitoramento são acessadas no menu **monitorar** na portal do Azure. Selecione **mais** na seção **insights** para abrir a página **visão geral** com os blocos de solução. O bloco **logs de atividade do Azure** exibe uma contagem do número de registros **AzureActivity** em seu espaço de trabalho.

![Mosaico de registos de atividades do Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no bloco **logs de atividade do Azure** para abrir a exibição **logs de atividade do Azure** . A exibição inclui as partes de visualização na tabela a seguir. Cada parte lista até 10 itens que correspondem aos critérios das partes para o intervalo de tempo especificado. Você pode executar uma consulta de log que retorna todos os registros correspondentes clicando em **Ver todos** na parte inferior da parte.

![Dashboard de registos de atividades do Azure](media/collect-activity-logs/activity-log-dash.png)

| Parte de visualização | Descrição |
| --- | --- |
| Entradas de registo de atividades do Azure | Mostra um gráfico de barras dos totais principais registros de entrada do log de atividades do Azure para o intervalo de datas selecionado e mostra uma lista dos 10 principais chamadores de atividade. Clique para executar uma pesquisa de registos para o gráfico de barras `AzureActivity`. Clique em um item do chamador para executar uma pesquisa de logs retornando todas as entradas do log de atividades para esse item. |
| Registos de atividades por Estado | Mostra um gráfico de rosca para o status do log de atividades do Azure para o intervalo de datas selecionado e uma lista dos dez principais registros de status. Clique no gráfico para executar uma consulta de log para `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Clique em um item de status para executar uma pesquisa de logs que retorna todas as entradas do log de atividades para esse registro de status. |
| Registos de atividades por recurso | Mostra o número total de recursos com logs de atividade e lista os dez principais recursos com contagens de registros para cada recurso. Clique na área total para executar uma pesquisa de registos para `AzureActivity | summarize AggregatedValue = count() by Resource`, que mostra todos os recursos do Azure disponíveis para a solução. Clique em um recurso para executar uma consulta de log que retorna todos os registros de atividade para esse recurso. |
| Registos de atividade pelo fornecedor de recursos | Mostra o número total de provedores de recursos que produzem logs de atividades e lista os dez principais. Clique na área total para executar uma consulta de log para `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, que mostra todos os provedores de recursos do Azure. Clique em um provedor de recursos para executar uma consulta de log que retorna todos os registros de atividade do provedor. |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [log de atividades](activity-logs-overview.md).
- Saiba mais sobre a [plataforma de dados Azure monitor](data-platform.md).
- Use [consultas de log](../log-query/log-query-overview.md) para exibir informações detalhadas do seu log de atividades.
