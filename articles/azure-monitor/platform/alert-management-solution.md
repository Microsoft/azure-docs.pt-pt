---
title: Solução de gestão do Azure Log Analytics de alertas | Documentos da Microsoft
description: A solução de gestão de alertas no Log Analytics ajuda a analisar todos os alertas no seu ambiente.  Além de consolidar alertas geradas no Log Analytics, ele importa alertas de grupos de gestão do System Center Operations Manager ligados para o Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 06532369efb802606eb13a4b38a8579a3528f999
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60777040"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Solução de gestão de alerta no Log Analytics do Azure

![Ícone de gestão de alertas](media/alert-management-solution/icon.png)

> [!NOTE]
>  Monitor do Azure agora suporta avançado de capacidades para [gerir os alertas em escala](https://aka.ms/azure-alerts-overview), inclusive aquelas geradas pelo [monitorização ferramentas como o SCOM, Zabbix ou Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


A solução de gestão de alertas ajuda a analisar todos os alertas no seu repositório do Log Analytics.  Estes alertas podem proveniente de uma variedade de origens, incluindo essas origens [criado pelo Log Analytics](../../azure-monitor/platform/alerts-overview.md) ou [importados a partir do Nagios ou Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). A solução importa também alertas a partir de qualquer [ligadas a grupos de gestão do System Center Operations Manager](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Pré-requisitos
A solução funciona com quaisquer registos no repositório do Log Analytics com o tipo de **alerta**, por isso, tem de efetuar qualquer configuração é necessária para recolher estes registos.

- Para os alertas do Log Analytics, [criar regras de alerta](../../azure-monitor/platform/alerts-overview.md) para criar registos de alerta diretamente no repositório.
- Para os alertas do Nagios e do Zabbix, [configurar esses servidores](../../azure-monitor/learn/quick-collect-linux-computer.md) para enviar alertas para o Log Analytics.
- Para os alertas do System Center Operations Manager, [ligar o seu grupo de gestão do Operations Manager à sua área de trabalho do Log Analytics](../../azure-monitor/platform/om-agents.md).  Todos os alertas criados no System Center Operations Manager são importados para o Log Analytics.  

## <a name="configuration"></a>Configuração
Adicionar a solução de gestão de alertas à sua área de trabalho do Log Analytics através do processo descrito em [adicionar soluções](../../azure-monitor/insights/solutions.md). Não há nenhuma configuração adicional.

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
| [Grupo de gestão do System Center Operations Manager](../../azure-monitor/platform/om-agents.md) |Sim |Alertas que são gerados em agentes do Operations Manager são entregues para o grupo de gestão e, depois, reencaminhadas para o Log Analytics.<br><br>Não é necessária uma conexão direta de agentes do Operations Manager ao Log Analytics. Dados de alertas são reencaminhados do grupo de gestão para o repositório do Log Analytics. |


### <a name="collection-frequency"></a>Frequência da recolha
- Alerta de registos que estão disponível para a solução assim que eles são armazenados no repositório.
- Dados de alerta são enviados do grupo de gestão do Operations Manager ao Log Analytics a cada três minutos.  

## <a name="using-the-solution"></a>Utilizar a solução
Quando adiciona a solução de gestão de alertas a sua área de trabalho do Log Analytics, o **gestão de alertas** mosaico é adicionado ao seu dashboard.  Este mosaico apresenta uma contagem e uma representação gráfica do número de alertas atualmente ativos que foram gerados nas últimas 24 horas.  Não é possível alterar este intervalo de tempo.

![Mosaico gestão de alertas](media/alert-management-solution/tile.png)

Clique nas **gestão de alertas** mosaico para abrir o **gestão de alertas** dashboard.  O dashboard inclui as colunas da tabela seguinte.  Cada coluna apresenta os alertas de 10 principais por contagem que satisfaçam os critérios dessa coluna para o âmbito especificado e o intervalo de tempo.  Pode executar uma pesquisa de registos que fornece toda a lista clicando **ver todas as** na parte inferior da coluna ou ao clicar no cabeçalho da coluna.

| Coluna | Descrição |
|:--- |:--- |
| Alertas Críticos |Todos os alertas com uma gravidade de crítico agrupado por nome do alerta.  Clique num nome de alerta para executar uma pesquisa de registos que devolva todos os registos para esse alerta. |
| Alertas de Aviso |Todos os alertas com uma gravidade de aviso, agrupada por nome do alerta.  Clique num nome de alerta para executar uma pesquisa de registos que devolva todos os registos para esse alerta. |
| Alertas SCOM Ativos |Todos os alertas recolhidos a partir do Operations Manager com qualquer Estado diferente de *fechado* agrupados por origem que gerou o alerta. |
| Todos os alertas ativos |Todos os alertas com qualquer gravidade agrupados por nome do alerta. Inclui apenas alertas do Operations Manager com qualquer Estado diferente de *fechado*. |

Se se deslocar para a direita, o dashboard apresenta uma lista de várias consultas comuns que pode clicar em para efetuar uma [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md) para dados de alertas.

![Dashboard de gestão de alertas](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução de gestão de alertas analisa qualquer registo com um tipo de **alerta**.  Alertas criado pelo Log Analytics ou recolhidos a partir de Nagios ou Zabbix não são recolhidas diretamente pela solução.

A solução de importar alertas do System Center Operations Manager e cria um registo correspondente para cada um com um tipo de **alerta** e um SourceSystem de **OpsManager**.  Estes registos têm as propriedades na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alertar* |
| SourceSystem |*OpsManager* |
| AlertContext |Detalhes do item de dados que causou o alerta a ser gerado no formato XML. |
| AlertDescription |Descrição detalhada do alerta. |
| AlertId |GUID do alerta. |
| AlertName |Nome do alerta. |
| AlertPriority |Nível de prioridade do alerta. |
| AlertSeverity |Nível de gravidade do alerta. |
| AlertState |Último Estado de resolução do alerta. |
| LastModifiedBy |Nome do utilizador que o alerta da última modificação. |
| ManagementGroupName |Nome do grupo de gestão em que o alerta foi gerado. |
| RepeatCount |Número de vezes que o mesmo alerta foi gerado para o mesmo monitorizado objeto desde a ser resolvidos. |
| ResolvedBy |Nome do utilizador que o alerta foi resolvido. Vazio se o alerta ainda não foi resolvido. |
| SourceDisplayName |Nome a apresentar do objecto de monitorização que gerou o alerta. |
| SourceFullName |Nome completo do objeto de monitorização que gerou o alerta. |
| TicketId |ID de pedido de suporte para o alerta se o ambiente do System Center Operations Manager está integrado com um processo para atribuir permissões para alertas.  ID vazio de nenhum pedido está atribuído. |
| TimeGenerated |Data e hora em que o alerta foi criado. |
| TimeLastModified |Data e hora em que o alerta foi alterado pela última vez. |
| TimeRaised |Data e hora em que o alerta foi gerado. |
| TimeResolved |Data e hora em que o alerta foi resolvido. Vazio se o alerta ainda não foi resolvido. |

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



## <a name="next-steps"></a>Passos Seguintes
* Veja o artigo [Alerts in Log Analytics](../../azure-monitor/platform/alerts-overview.md) (Alertas no Log Analytics) para obter detalhes sobre a geração de alertas do Log Analytics.
