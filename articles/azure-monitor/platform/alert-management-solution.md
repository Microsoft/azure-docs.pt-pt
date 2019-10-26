---
title: Gerenciamento de Alertas solução no Azure Log Analytics | Microsoft Docs
description: A solução Gerenciamento de Alertas no Log Analytics ajuda a analisar todos os alertas em seu ambiente.  Além de consolidar alertas gerados dentro de Log Analytics, ele importa alertas de grupos de gerenciamento de System Center Operations Manager conectados para Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: fe484d8b5a06946b844acb5e506ec4dcc99ebc23
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932734"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Gerenciamento de Alertas solução no Azure Log Analytics

![Ícone de Gerenciamento de Alertas](media/alert-management-solution/icon.png)

> [!NOTE]
>  O Azure Monitor agora dá suporte a recursos aprimorados para [gerenciar seus alertas em escala](https://aka.ms/azure-alerts-overview), incluindo aqueles gerados por [ferramentas de monitoramento como System Center Operations Manager, Zabbix ou Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


A solução Gerenciamento de Alertas ajuda a analisar todos os alertas em seu repositório de Log Analytics.  Esses alertas podem ter vindo de uma variedade de fontes, incluindo as fontes [criadas por log Analytics](../../azure-monitor/platform/alerts-overview.md) ou [importadas de Nagios ou Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). A solução também importa alertas de todos os [grupos de gerenciamento de System Center Operations Manager conectados](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Pré-requisitos
A solução funciona com qualquer registro no repositório de Log Analytics com um tipo de **alerta**, portanto, você deve executar qualquer configuração necessária para coletar esses registros.

- Para Log Analytics alertas, [crie regras de alerta](../../azure-monitor/platform/alerts-overview.md) para criar registros de alerta diretamente no repositório.
- Para alertas de Nagios e Zabbix, [Configure esses servidores](../../azure-monitor/learn/quick-collect-linux-computer.md) para enviar alertas para log Analytics.
- Para System Center Operations Manager alertas, [Conecte seu grupo de gerenciamento de Operations Manager ao seu espaço de trabalho do log Analytics](../../azure-monitor/platform/om-agents.md).  Todos os alertas criados no System Center Operations Manager são importados para o Log Analytics.  

## <a name="configuration"></a>Configuração
Adicione a solução Gerenciamento de Alertas ao seu espaço de trabalho do Log Analytics usando o processo descrito em [Adicionar soluções](../../azure-monitor/insights/solutions.md). Não há nenhuma configuração adicional.

## <a name="management-packs"></a>Pacotes de gestão
Se o grupo de gerenciamento do System Center Operations Manager estiver conectado ao seu espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no System Center Operations Manager quando você adicionar essa solução.  Não há nenhuma configuração ou manutenção dos pacotes de gerenciamento necessários.

* Microsoft System Center Advisor Gerenciamento de Alertas (Microsoft. IntelligencePacks. AlertManagement)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Recolha de dados
### <a name="agents"></a>Agentes
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|:--- |:--- |:--- |
| [Agentes do Windows](agent-windows.md) | Não |Agentes diretos do Windows não geram alertas.  Log Analytics alertas podem ser criados a partir de eventos e dados de desempenho coletados de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não |Agentes diretos do Linux não geram alertas.  Log Analytics alertas podem ser criados a partir de eventos e dados de desempenho coletados de agentes do Linux.  Os alertas de Nagios e Zabbix são coletados desses servidores que exigem o agente do Linux. |
| [System Center Operations Manager grupo de gerenciamento](../../azure-monitor/platform/om-agents.md) |Sim |Alertas gerados em agentes de Operations Manager são entregues ao grupo de gerenciamento e, em seguida, encaminhados para Log Analytics.<br><br>Uma conexão direta de agentes de Operations Manager para Log Analytics não é necessária. Os dados de alerta são encaminhados do grupo de gerenciamento para o repositório de Log Analytics. |


### <a name="collection-frequency"></a>Frequência da recolha
- Os registros de alerta estão disponíveis para a solução assim que são armazenados no repositório.
- Os dados de alerta são enviados do grupo de gerenciamento de Operations Manager para Log Analytics a cada três minutos.  

## <a name="using-the-solution"></a>Utilizar a solução
Quando você adiciona a solução de Gerenciamento de Alertas ao seu espaço de trabalho do Log Analytics, o bloco **Gerenciamento de alertas** é adicionado ao seu painel.  Esse bloco exibe uma contagem e uma representação gráfica do número de alertas atualmente ativos que foram gerados nas últimas 24 horas.  Você não pode alterar esse intervalo de tempo.

![Bloco Gerenciamento de Alertas](media/alert-management-solution/tile.png)

Clique no bloco **Gerenciamento de alertas** para abrir o painel de **Gerenciamento de alertas** .  O dashboard inclui as colunas da tabela seguinte.  Cada coluna lista os 10 principais alertas por contagem que correspondem aos critérios da coluna para o escopo e o intervalo de tempo especificados.  Você pode executar uma pesquisa de logs que fornece a lista inteira clicando em **Ver todos** na parte inferior da coluna ou clicando no cabeçalho da coluna.

| Column | Descrição |
|:--- |:--- |
| Alertas Críticos |Todos os alertas com severidade crítica agrupadas por nome do alerta.  Clique em um nome de alerta para executar uma pesquisa de logs que retorna todos os registros desse alerta. |
| Alertas de aviso |Todos os alertas com uma severidade de aviso agrupados por nome do alerta.  Clique em um nome de alerta para executar uma pesquisa de logs que retorna todos os registros desse alerta. |
| Alertas do Active System Center Operations Manager |Todos os alertas coletados de Operations Manager com qualquer estado diferente de *fechado* agrupados por origem que gerou o alerta. |
| Todos os alertas ativos |Todos os alertas com qualquer severidade agrupada por nome do alerta. Inclui apenas Operations Manager alertas com qualquer estado diferente de *Closed*. |

Se você rolar para a direita, o painel listará várias consultas comuns nas quais você pode clicar para executar uma [pesquisa de log](../../azure-monitor/log-query/log-query-overview.md) para dados de alerta.

![Painel do Gerenciamento de Alertas](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução Gerenciamento de Alertas analisa qualquer registro com um tipo de **alerta**.  Os alertas criados por Log Analytics ou coletados de Nagios ou Zabbix não são coletados diretamente pela solução.

A solução importa alertas de System Center Operations Manager e cria um registro correspondente para cada um com um tipo de **alerta** e um SourceSystem de **OpsManager**.  Esses registros têm as propriedades na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*Alerta* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Detalhes do item de dados que fez com que o alerta fosse gerado no formato XML. |
| `AlertDescription` |Descrição detalhada do alerta. |
| `AlertId` |GUID do alerta. |
| `AlertName` |Nome do alerta. |
| `AlertPriority` |Nível de prioridade do alerta. |
| `AlertSeverity` |Nível de severidade do alerta. |
| `AlertState` |Estado de resolução mais recente do alerta. |
| `LastModifiedBy` |Nome do usuário que modificou o alerta pela última vez. |
| `ManagementGroupName` |Nome do grupo de gerenciamento em que o alerta foi gerado. |
| `RepeatCount` |Número de vezes que o mesmo alerta foi gerado para o mesmo objeto monitorado, pois está sendo resolvido. |
| `ResolvedBy` |Nome do usuário que resolveu o alerta. Vazio se o alerta ainda não tiver sido resolvido. |
| `SourceDisplayName` |Nome de exibição do objeto de monitoramento que gerou o alerta. |
| `SourceFullName` |Nome completo do objeto de monitoramento que gerou o alerta. |
| `TicketId` |ID do tíquete do alerta se o ambiente de System Center Operations Manager estiver integrado a um processo de atribuição de tíquetes para alertas.  Não há ID de tíquete vazia. |
| `TimeGenerated` |Data e hora em que o alerta foi criado. |
| `TimeLastModified` |Data e hora em que o alerta foi alterado pela última vez. |
| `TimeRaised` |Data e hora em que o alerta foi gerado. |
| `TimeResolved` |Data e hora em que o alerta foi resolvido. Vazio se o alerta ainda não tiver sido resolvido. |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela a seguir fornece pesquisas de log de exemplo para os registros de alerta coletados por essa solução: 

| Consulta | Descrição |
|:---|:---|
| Alertar &#124; em que SourceSystem = = "OpsManager" e AlertSeverity = = "Error" e timeelevated > atrás (24h) |Alertas críticos gerados durante as últimas 24 horas |
| Alertar &#124; onde AlertSeverity = = "Warning" e timeraiseed > atrás (24h) |Alertas de aviso gerados durante as últimas 24 horas |
| Alertar &#124; em que SourceSystem = = "OpsManager" e alertout! = "closed" e timeelevated > atrás ( &#124; 24h) countment contagem = Count () por SourceDisplayName |Fontes com alertas ativos gerados durante as últimas 24 horas |
| Alertar &#124; em que SourceSystem = = "OpsManager" e AlertSeverity = = "Error" e timeelevated > atrás (24h) e alertstate! = "closed" |Alertas críticos gerados nas últimas 24 horas que ainda estão ativos |
| Alertar &#124; onde SourceSystem = = "OpsManager" e timeelevated > atrás (24h) e alertstate = = "closed" |Alertas gerados nas últimas 24 horas que agora estão fechados |
| Alerta &#124; em que SourceSystem = = "OpsManager" e timeelevated > atrás (1D &#124; ) resumir contagem = Count () por AlertSeverity |Alertas gerados durante os últimos 1 dias agrupados por sua severidade |
| Alertar &#124; onde SourceSystem = = "OpsManager" e timeelevated > atrás (1D &#124; ) classificar por repeatCount desc |Alertas gerados durante os últimos 1 dia classificados por seu valor de contagem de repetição |



## <a name="next-steps"></a>Passos seguintes
* Veja o artigo [Alerts in Log Analytics](../../azure-monitor/platform/alerts-overview.md) (Alertas no Log Analytics) para obter detalhes sobre a geração de alertas do Log Analytics.
