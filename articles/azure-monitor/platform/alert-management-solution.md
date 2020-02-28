---
title: Solução de gestão do Azure Log Analytics de alertas | Documentos da Microsoft
description: A solução de gestão de alertas no Log Analytics ajuda a analisar todos os alertas no seu ambiente.  Além de consolidar alertas geradas no Log Analytics, ele importa alertas de grupos de gestão do System Center Operations Manager ligados para o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668388"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Solução de gestão de alerta no Log Analytics do Azure

![Ícone de gestão de alertas](media/alert-management-solution/icon.png)

> [!NOTE]
>  O Azure Monitor suporta agora capacidades reforçadas para [gerir os seus alertas em escala](https://aka.ms/azure-alerts-overview), incluindo os gerados por ferramentas de [monitorização como o System Center Operations Manager, Zabbix ou Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


A solução de gestão de alertas ajuda a analisar todos os alertas no seu repositório do Log Analytics.  Estes alertas podem ter vindo de uma variedade de fontes, incluindo as fontes [criadas pela Log Analytics](../../azure-monitor/platform/alerts-overview.md) ou [importadas de Nagios ou Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). A solução também importa alertas de quaisquer grupos de gestão de Gestão de Operações do [System Center conectados.](../../azure-monitor/platform/om-agents.md)

## <a name="prerequisites"></a>Pré-requisitos
A solução funciona com quaisquer registos no repositório Log Analytics com um tipo de **Alerta,** pelo que deve realizar qualquer configuração necessária para recolher estes registos.

- Para os alertas de Log Analytics, [crie regras](../../azure-monitor/platform/alerts-overview.md) de alerta para criar registos de alerta diretamente no repositório.
- Para os alertas Nagios e Zabbix, [configure os servidores](../../azure-monitor/learn/quick-collect-linux-computer.md) para enviar alertas para o Log Analytics.
- Para alertas de Gestor de Operações do System Center, ligue o seu grupo de [gestão de Gestão](../../azure-monitor/platform/om-agents.md)de Operações ao seu espaço de trabalho Log Analytics .  Todos os alertas criados no System Center Operations Manager são importados para o Log Analytics.  

## <a name="configuration"></a>Configuração
Adicione a solução de Gestão de Alerta ao seu espaço de trabalho Log Analytics utilizando o processo descrito em [soluções Add](../../azure-monitor/insights/solutions.md). Não há nenhuma configuração adicional.

## <a name="management-packs"></a>Pacotes de gestão
Se o grupo de gestão do System Center Operations Manager estiver ligado à sua área de trabalho do Log Analytics, os seguintes pacotes de gestão são instalados no System Center Operations Manager quando adicionar esta solução.  Não existe nenhuma configuração ou a manutenção dos pacotes de gestão necessário.

* Gestão de alertas do Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Recolha de dados
### <a name="agents"></a>Agentes
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|:--- |:--- |:--- |
| [Agentes do Windows](agent-windows.md) | Não |Agentes diretos do Windows não geram alertas.  Alertas do log Analytics podem ser criadas a partir de eventos e dados de desempenho recolhidos a partir de Windows agentes. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não |Agentes diretos do Linux não geram alertas.  Alertas do log Analytics podem ser criadas do eventos e dados de desempenho recolhidas pelos agentes do Linux.  Alertas do Nagios e do Zabbix são recolhidas a partir desses servidores que exigem o agente do Linux. |
| [Grupo de gestão de gestão de gestão de gestão de operações do Centro de Sistema](../../azure-monitor/platform/om-agents.md) |Sim |Alertas que são gerados em agentes do Operations Manager são entregues para o grupo de gestão e, depois, reencaminhadas para o Log Analytics.<br><br>Não é necessária uma conexão direta de agentes do Operations Manager ao Log Analytics. Dados de alertas são reencaminhados do grupo de gestão para o repositório do Log Analytics. |


### <a name="collection-frequency"></a>Frequência da recolha
- Alerta de registos que estão disponível para a solução assim que eles são armazenados no repositório.
- Dados de alerta são enviados do grupo de gestão do Operations Manager ao Log Analytics a cada três minutos.  

## <a name="using-the-solution"></a>Utilizar a solução
Quando adicionar a solução de Gestão de Alerta ao seu espaço de trabalho Log Analytics, o azulejo de **Gestão** de Alerta é adicionado ao seu painel de instrumentos.  Este mosaico apresenta uma contagem e uma representação gráfica do número de alertas atualmente ativos que foram gerados nas últimas 24 horas.  Não é possível alterar este intervalo de tempo.

![Mosaico gestão de alertas](media/alert-management-solution/tile.png)

Clique no azulejo de **Gestão** de Alerta para abrir o painel de gestão de **alerta.**  O dashboard inclui as colunas da tabela seguinte.  Cada coluna apresenta os alertas de 10 principais por contagem que satisfaçam os critérios dessa coluna para o âmbito especificado e o intervalo de tempo.  Pode executar uma pesquisa de registo que fornece toda a lista clicando **Para ver tudo** na parte inferior da coluna ou clicando no cabeçalho da coluna.

| Coluna | Descrição |
|:--- |:--- |
| Alertas Críticos |Todos os alertas com uma gravidade de crítico agrupado por nome do alerta.  Clique num nome de alerta para executar uma pesquisa de registos que devolva todos os registos para esse alerta. |
| Alertas de Aviso |Todos os alertas com uma gravidade de aviso, agrupada por nome do alerta.  Clique num nome de alerta para executar uma pesquisa de registos que devolva todos os registos para esse alerta. |
| Alertas de gestor de operações do Centro de Sistema ativo |Todos os alertas recolhidos do Gestor de Operações com qualquer outro estado que não *o Fechado* agrupado por fonte que gerou o alerta. |
| Todos os alertas ativos |Todos os alertas com qualquer gravidade agrupados por nome do alerta. Apenas inclui alertas do Gestor de Operações com qualquer outro estado que não *o Fechado.* |

Se deslocar para a direita, o painel de instrumentos lista várias consultas comuns em que pode clicar para realizar uma pesquisa de [registo](../../azure-monitor/log-query/log-query-overview.md) de dados de alerta.

![Dashboard de gestão de alertas](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução de Gestão de Alerta analisa qualquer registo com um tipo de **Alerta.**  Alertas criado pelo Log Analytics ou recolhidos a partir de Nagios ou Zabbix não são recolhidas diretamente pela solução.

A solução importa alertas do System Center Operations Manager e cria um registo correspondente para cada um com um tipo de **Alerta** e um SourceSystem de **OpsManager**.  Estes registos têm as propriedades na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*Alerta* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Detalhes do item de dados que causou o alerta a ser gerado no formato XML. |
| `AlertDescription` |Descrição detalhada do alerta. |
| `AlertId` |GUID do alerta. |
| `AlertName` |Nome do alerta. |
| `AlertPriority` |Nível de prioridade do alerta. |
| `AlertSeverity` |Nível de gravidade do alerta. |
| `AlertState` |Último Estado de resolução do alerta. |
| `LastModifiedBy` |Nome do utilizador que o alerta da última modificação. |
| `ManagementGroupName` |Nome do grupo de gestão em que o alerta foi gerado. |
| `RepeatCount` |Número de vezes que o mesmo alerta foi gerado para o mesmo monitorizado objeto desde a ser resolvidos. |
| `ResolvedBy` |Nome do utilizador que o alerta foi resolvido. Vazio se o alerta ainda não foi resolvido. |
| `SourceDisplayName` |Nome a apresentar do objecto de monitorização que gerou o alerta. |
| `SourceFullName` |Nome completo do objeto de monitorização que gerou o alerta. |
| `TicketId` |ID de pedido de suporte para o alerta se o ambiente do System Center Operations Manager está integrado com um processo para atribuir permissões para alertas.  ID vazio de nenhum pedido está atribuído. |
| `TimeGenerated` |Data e hora em que o alerta foi criado. |
| `TimeLastModified` |Data e hora em que o alerta foi alterado pela última vez. |
| `TimeRaised` |Data e hora em que o alerta foi gerado. |
| `TimeResolved` |Data e hora em que o alerta foi resolvido. Vazio se o alerta ainda não foi resolvido. |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte disponibiliza pesquisas de registos de exemplo para esta solução recolhidas registos de alerta: 

| Consulta | Descrição |
|:---|:---|
| Alerta &#124; onde SourceSystem = = "OpsManager" e AlertSeverity = = "erro" e TimeRaised > ago(24h) |Alertas críticos gerados nas últimas 24 horas |
| Alerta &#124; onde AlertSeverity = = "aviso" e TimeRaised > ago(24h) |Alertas de aviso gerados nas últimas 24 horas |
| Alerta &#124; onde SourceSystem = = "OpsManager" e AlertState! = "Fechado" e TimeRaised > ago(24h) &#124; resumir contagem = count () by SourceDisplayName |Origens com alertas ativos gerados nas últimas 24 horas |
| Alerta &#124; onde SourceSystem = = "OpsManager" e AlertSeverity = = "erro" e TimeRaised > ago(24h) e AlertState! = "Fechado" |Alertas críticos gerados nas últimas 24 horas que ainda estão ativas |
| Alerta &#124; onde SourceSystem = = "OpsManager" e TimeRaised > ago(24h) e AlertState = = "Fechado" |Alertas gerados nas últimas 24 horas que agora estão fechadas |
| Alerta &#124; onde SourceSystem = = "OpsManager" e TimeRaised > ago(1d) &#124; resumir contagem = count () by AlertSeverity |Alertas gerados durante o último dia, agrupados pelo respetivo grau de gravidade |
| Alerta &#124; onde SourceSystem = = "OpsManager" e TimeRaised > ago(1d) &#124; ordenar por RepeatCount desc |Alertas gerados durante o último dia, ordenados pelo seu valor de contagem de repetição |



## <a name="next-steps"></a>Passos seguintes
* Veja o artigo [Alerts in Log Analytics](../../azure-monitor/platform/alerts-overview.md) (Alertas no Log Analytics) para obter detalhes sobre a geração de alertas do Log Analytics.
