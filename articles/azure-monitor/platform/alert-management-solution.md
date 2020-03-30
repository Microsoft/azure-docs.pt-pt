---
title: Solução de Gestão de Alerta no Azure Log Analytics [ Microsoft Docs
description: A solução de Gestão de Alertas em Log Analytics ajuda-o a analisar todos os alertas no seu ambiente.  Além de consolidar alertas gerados no Log Analytics, importa alertas de grupos de gestão de gestores de operações do System Center conectados para o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668388"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Solução de Gestão de Alerta no Azure Log Analytics

![Ícone de Gestão de Alerta](media/alert-management-solution/icon.png)

> [!NOTE]
>  O Azure Monitor suporta agora capacidades reforçadas para [gerir os seus alertas em escala](https://aka.ms/azure-alerts-overview), incluindo os gerados por ferramentas de [monitorização como o System Center Operations Manager, Zabbix ou Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


A solução de Gestão de Alertaajuda-o a analisar todos os alertas no seu repositório Log Analytics.  Estes alertas podem ter vindo de uma variedade de fontes, incluindo as fontes [criadas pela Log Analytics](../../azure-monitor/platform/alerts-overview.md) ou [importadas de Nagios ou Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). A solução também importa alertas de quaisquer grupos de gestão de Gestão de Operações do [System Center conectados.](../../azure-monitor/platform/om-agents.md)

## <a name="prerequisites"></a>Pré-requisitos
A solução funciona com quaisquer registos no repositório Log Analytics com um tipo de **Alerta,** pelo que deve realizar qualquer configuração necessária para recolher estes registos.

- Para os alertas de Log Analytics, [crie regras](../../azure-monitor/platform/alerts-overview.md) de alerta para criar registos de alerta diretamente no repositório.
- Para os alertas Nagios e Zabbix, [configure os servidores](../../azure-monitor/learn/quick-collect-linux-computer.md) para enviar alertas para o Log Analytics.
- Para alertas de Gestor de Operações do System Center, ligue o seu grupo de [gestão de Gestão](../../azure-monitor/platform/om-agents.md)de Operações ao seu espaço de trabalho Log Analytics .  Quaisquer alertas criados no System Center Operations Manager são importados para o Log Analytics.  

## <a name="configuration"></a>Configuração
Adicione a solução de Gestão de Alerta ao seu espaço de trabalho Log Analytics utilizando o processo descrito em [soluções Add](../../azure-monitor/insights/solutions.md). Não há nenhuma configuração adicional.

## <a name="management-packs"></a>Pacotes de gestão
Se o seu grupo de gestão de Gestão de Operações do System Center estiver ligado ao seu espaço de trabalho Log Analytics, os seguintes pacotes de gestão são instalados no System Center Operations Manager quando adicionar esta solução.  Não é necessária qualquer configuração ou manutenção dos pacotes de gestão.

* Microsoft System Center Advisor Advisor Management (Microsoft.IntelligencePacks.AlertManagement)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Recolha de dados
### <a name="agents"></a>Agentes
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|:--- |:--- |:--- |
| [Agentes do Windows](agent-windows.md) | Não |Os agentes diretos do Windows não geram alertas.  Os alertas de Log Analytics podem ser criados a partir de eventos e dados de desempenho recolhidos a partir de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não |Os agentes diretos do Linux não geram alertas.  Os alertas de Log Analytics podem ser criados a partir de eventos e dados de desempenho recolhidos a partir de agentes Linux.  Os alertas de Nagios e Zabbix são recolhidos dos servidores que requerem o agente Linux. |
| [Grupo de gestão do System Center Operations Manager](../../azure-monitor/platform/om-agents.md) |Sim |Os alertas gerados nos agentes do Gestor de Operações são entregues ao grupo de gestão e depois encaminhados para o Log Analytics.<br><br>Não é necessária uma ligação direta dos agentes do Gestor de Operações ao Log Analytics. Os dados de alerta são encaminhados do grupo de gestão para o repositório Log Analytics. |


### <a name="collection-frequency"></a>Frequência da recolha
- Os registos de alerta estão disponíveis para a solução assim que forem armazenados no repositório.
- Os dados de alerta são enviados do grupo de gestão do Gestor de Operações para registar o Analytics a cada três minutos.  

## <a name="using-the-solution"></a>Utilizar a solução
Quando adicionar a solução de Gestão de Alerta ao seu espaço de trabalho Log Analytics, o azulejo de **Gestão** de Alerta é adicionado ao seu painel de instrumentos.  Este azulejo apresenta uma contagem e representação gráfica do número de alertas atualmente ativos que foram gerados nas últimas 24 horas.  Não pode mudar este intervalo de tempo.

![Telha de Gestão de Alerta](media/alert-management-solution/tile.png)

Clique no azulejo de **Gestão** de Alerta para abrir o painel de gestão de **alerta.**  O dashboard inclui as colunas da tabela seguinte.  Cada coluna lista os 10 alertas superiores por contagem correspondente aos critérios dessa coluna para o intervalo de tempo e alcance especificado.  Pode executar uma pesquisa de registo que fornece toda a lista clicando **Para ver tudo** na parte inferior da coluna ou clicando no cabeçalho da coluna.

| Coluna | Descrição |
|:--- |:--- |
| Alertas Críticos |Todos os alertas com uma gravidade da Critical agruparam-se pelo nome de alerta.  Clique num nome de alerta para fazer uma pesquisa de registo devolvendo todos os registos para esse alerta. |
| Alertas de alerta |Todos os alertas com gravidade de Aviso agrupados pelo nome de alerta.  Clique num nome de alerta para fazer uma pesquisa de registo devolvendo todos os registos para esse alerta. |
| Alertas de gestor de operações do Centro de Sistema ativo |Todos os alertas recolhidos do Gestor de Operações com qualquer outro estado que não *o Fechado* agrupado por fonte que gerou o alerta. |
| Todos os Alertas Ativos |Todos os alertas com qualquer gravidade agruparado pelo nome de alerta. Apenas inclui alertas do Gestor de Operações com qualquer outro estado que não *o Fechado.* |

Se deslocar para a direita, o painel de instrumentos lista várias consultas comuns em que pode clicar para realizar uma pesquisa de [registo](../../azure-monitor/log-query/log-query-overview.md) de dados de alerta.

![Painel de gestão de alerta](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução de Gestão de Alerta analisa qualquer registo com um tipo de **Alerta.**  Os alertas criados pelo Log Analytics ou recolhidos pela Nagios ou zabbix não são recolhidos diretamente pela solução.

A solução importa alertas do System Center Operations Manager e cria um registo correspondente para cada um com um tipo de **Alerta** e um SourceSystem de **OpsManager**.  Estes registos têm as propriedades na tabela seguinte:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*Alerta* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Detalhes do item de dados que fez com que o alerta fosse gerado em formato XML. |
| `AlertDescription` |Descrição detalhada do alerta. |
| `AlertId` |GUIA do alerta. |
| `AlertName` |Nome do alerta. |
| `AlertPriority` |Nível prioritário do alerta. |
| `AlertSeverity` |Nível de gravidade do alerta. |
| `AlertState` |Última resolução estado do alerta. |
| `LastModifiedBy` |Nome do utilizador que modificou o alerta pela última vez. |
| `ManagementGroupName` |Nome do grupo de gestão onde o alerta foi gerado. |
| `RepeatCount` |O número de vezes o mesmo alerta foi gerado para o mesmo objeto monitorizado desde que foi resolvido. |
| `ResolvedBy` |Nome do utilizador que resolveu o alerta. Vazio se o alerta ainda não tiver sido resolvido. |
| `SourceDisplayName` |Mostrar o nome do objeto de monitorização que gerou o alerta. |
| `SourceFullName` |Nome completo do objeto de monitorização que gerou o alerta. |
| `TicketId` |ID do bilhete para o alerta se o ambiente do Gestor de Operações do Centro de Sistema estiver integrado com um processo de atribuição de bilhetes para alertas.  Vazio sem identificação de bilhete está atribuído. |
| `TimeGenerated` |Data e hora em que o alerta foi criado. |
| `TimeLastModified` |Data e hora que o alerta foi alterado pela última vez. |
| `TimeRaised` |Data e hora que o alerta foi gerado. |
| `TimeResolved` |Data e hora em que o alerta foi resolvido. Vazio se o alerta ainda não tiver sido resolvido. |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte fornece pesquisas de registo de amostras para registos de alerta recolhidos por esta solução: 

| Consulta | Descrição |
|:---|:---|
| Alerta &#124; onde o SourceSystem == "OpsManager" e AlertSeverity == "error" e TimeRaised > atrás (24h) |Alertas críticos levantados nas últimas 24 horas |
| Alerta &#124; onde alertaseveridade == "aviso" e > timeraised há 24h(24h) |Alertas de alerta levantados nas últimas 24 horas |
| Alerta &#124; onde SourceSystem == "OpsManager" e AlertState != "Fechado" e TimeRaised >(24h) &#124; resumir Contagem = contagem() por SourceDisplayName |Fontes com alertas ativos levantados nas últimas 24 horas |
| Alerta &#124; onde SourceSystem == "OpsManager" e AlertSeverity == "error" e TimeRaised >(24h) e AlertState != "Fechado" |Alertas críticos levantados nas últimas 24 horas que ainda estão ativos |
| Alerta &#124; onde SourceSystem == "OpsManager" e TimeRaised > (24h) e AlertState == "Fechado" |Alertas levantados nas últimas 24 horas que estão agora encerrados |
| Alerta &#124; onde SourceSystem == "OpsManager" e TimeRaised >(1d) &#124; resumir Contagem = contagem() por AlertSeverity |Alertas levantados durante o último dia agrupados pela sua gravidade |
| Alerta &#124; onde sourceSystem == "OpsManager" e TimeRaised >(1d) &#124; por RepeatCount desc |Alertas levantados durante o último dia ordenados pelo seu valor de contagem repetida |



## <a name="next-steps"></a>Passos seguintes
* Veja o artigo [Alerts in Log Analytics](../../azure-monitor/platform/alerts-overview.md) (Alertas no Log Analytics) para obter detalhes sobre a geração de alertas do Log Analytics.
