---
title: Solução de Gestão de Alerta em Azure Log Analytics / Microsoft Docs
description: A solução de Gestão de Alerta no Log Analytics ajuda-o a analisar todos os alertas no seu ambiente.  Além de consolidar os alertas gerados no Log Analytics, importa alertas de grupos de gestão de operações do System Center para o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: adc29916c6b674531d7b0e8fcdd4e151b4a17bde
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677578"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Solução de Gestão de Alerta em Azure Log Analytics

![Ícone de gestão de alerta](media/alert-management-solution/icon.png)

A solução Gestão de Alerta ajuda-o a analisar todos os alertas no seu repositório Log Analytics.  Estes alertas podem ter vindo de uma variedade de fontes, incluindo as fontes [criadas pela Log Analytics](./alerts-overview.md) ou [importadas de Nagios ou Zabbix.](../learn/quick-collect-linux-computer.md) A solução também importa alertas de quaisquer [grupos de gestão de gestores](./om-agents.md)de operações do System Center ligados.

## <a name="prerequisites"></a>Pré-requisitos
A solução funciona com quaisquer registos no repositório Log Analytics com um tipo de **Alerta,** pelo que deve realizar qualquer configuração necessária para recolher estes registos.

- Para alertas de Log Analytics, [crie regras de alerta](./alerts-overview.md) para criar registos de alerta diretamente no repositório.
- Para alertas de Nagios e Zabbix, [configuure esses servidores](../learn/quick-collect-linux-computer.md) para enviar alertas para o Log Analytics.
- Para alertas do Gestor de Operações do Centro de Sistema, [ligue o seu grupo de gestão de Gestor de Operações ao seu espaço de trabalho Log Analytics.](./om-agents.md)  Quaisquer alertas criados no Gestor de Operações do Centro de Sistema são importados para o Log Analytics.  

## <a name="configuration"></a>Configuração
Adicione a solução de Gestão de Alerta ao seu espaço de trabalho Log Analytics utilizando o processo descrito em [soluções Add](../insights/solutions.md). Não há nenhuma configuração adicional.

## <a name="management-packs"></a>Pacotes de gestão
Se o seu grupo de gestão de operações do System Center Operations Manager estiver ligado ao seu espaço de trabalho Log Analytics, então os seguintes pacotes de gestão são instalados no Gestor de Operações do System Center quando adicionar esta solução.  Não existe configuração ou manutenção dos pacotes de gestão necessários.

* Microsoft System Center Advisor Alert Management (Microsoft.IntelligencePacks.AlertManagement)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](./om-agents.md).

## <a name="data-collection"></a>Recolha de dados
### <a name="agents"></a>Agentes
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|:--- |:--- |:--- |
| [Agentes do Windows](agent-windows.md) | Não |Os agentes do Windows diretos não geram alertas.  Os alertas do Log Analytics podem ser criados a partir de eventos e dados de desempenho recolhidos a partir de agentes do Windows. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não |Os agentes Linux diretos não geram alertas.  Os alertas do Log Analytics podem ser criados a partir de eventos e dados de desempenho recolhidos a partir de agentes Linux.  Os alertas de Nagios e Zabbix são recolhidos dos servidores que requerem o agente Linux. |
| [Grupo de gestão do System Center Operations Manager](./om-agents.md) |Sim |Os alertas gerados pelos agentes do Gestor de Operações são entregues ao grupo de gestão e depois encaminhados para o Log Analytics.<br><br>Não é necessária uma ligação direta dos agentes do Gestor de Operações ao Log Analytics. Os dados de alerta são reencaminhados do grupo de gestão para o repositório Log Analytics. |


### <a name="collection-frequency"></a>Frequência da recolha
- Os registos de alerta estão disponíveis para a solução assim que são armazenados no repositório.
- Os dados de alerta são enviados do grupo de gestão do Gestor de Operações para Registar Analytics a cada três minutos.  

## <a name="using-the-solution"></a>Utilizar a solução
Quando adiciona a solução de Gestão de Alerta ao seu espaço de trabalho Log Analytics, o azulejo **de Gestão** de Alerta é adicionado ao seu painel de instrumentos.  Este azulejo apresenta uma contagem e representação gráfica do número de alertas atualmente ativos que foram gerados nas últimas 24 horas.  Não pode mudar este intervalo de tempo.

![Azulejo de Gestão de Alerta](media/alert-management-solution/tile.png)

Clique no azulejo de **Gestão** de Alerta para abrir o painel **de gestão** de alerta.  O dashboard inclui as colunas da tabela seguinte.  Cada coluna lista os 10 melhores alertas por contagem que corresponde aos critérios dessa coluna para o âmbito e intervalo de tempo especificados.  Pode executar uma pesquisa de registo que forneça toda a lista clicando em **Ver tudo** na parte inferior da coluna ou clicando no cabeçalho da coluna.

| Coluna | Descrição |
|:--- |:--- |
| Alertas Críticos |Todos os alertas com uma gravidade da Critical agrupados por nome de alerta.  Clique num nome de alerta para executar uma pesquisa de registo retornando todos os registos para esse alerta. |
| Alertas de Aviso |Todos os alertas com uma gravidade de Aviso agrupados por nome de alerta.  Clique num nome de alerta para executar uma pesquisa de registo retornando todos os registos para esse alerta. |
| Alertas de gestores de operações do Centro de Sistema Ativo |Todos os alertas recolhidos do Gestor de Operações com qualquer outro estado que não *o Fechado* agrupados por fonte que gerou o alerta. |
| Todos os alertas ativos |Todos os alertas com qualquer gravidade agrupada pelo nome de alerta. Inclui apenas alertas do Gestor de Operações com qualquer estado que não *o Fechado.* |

Se deslocar para a direita, o painel de instrumentos lista várias consultas comuns em que pode clicar para realizar uma [pesquisa de registo](../log-query/log-query-overview.md) de dados de alerta.

![Painel de gestão de alerta](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução De Gestão de Alerta analisa qualquer registo com um tipo de **Alerta.**  Os alertas criados pela Log Analytics ou recolhidos de Nagios ou Zabbix não são recolhidos diretamente pela solução.

A solução importa alertas do Gestor de Operações do Centro de Sistema e cria um registo correspondente para cada um com um tipo de **Alerta** e um Sistema de Fonte de **OpsManager.**  Estes registos têm as propriedades na tabela seguinte:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*Alerta* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Detalhes do item de dados que fez com que o alerta fosse gerado em formato XML. |
| `AlertDescription` |Descrição detalhada do alerta. |
| `AlertId` |GUIA do alerta. |
| `AlertName` |O nome do alerta. |
| `AlertPriority` |Nível prioritário do alerta. |
| `AlertSeverity` |Nível de gravidade do alerta. |
| `AlertState` |Última resolução estado de alerta. |
| `LastModifiedBy` |Nome do utilizador que modificou o alerta pela última vez. |
| `ManagementGroupName` |Nome do grupo de gestão onde o alerta foi gerado. |
| `RepeatCount` |O número de vezes que o mesmo alerta foi gerado para o mesmo objeto monitorizado desde que foi resolvido. |
| `ResolvedBy` |Nome do utilizador que resolveu o alerta. Vazio se o alerta ainda não tiver sido resolvido. |
| `SourceDisplayName` |Mostrar o nome do objeto de monitorização que gerou o alerta. |
| `SourceFullName` |Nome completo do objeto de monitorização que gerou o alerta. |
| `TicketId` |Identificação de bilhetes para o alerta se o ambiente do System Center Operations Manager estiver integrado com um processo de atribuição de bilhetes para alertas.  Não está atribuído nenhum bilhete de identificação. |
| `TimeGenerated` |Data e hora em que o alerta foi criado. |
| `TimeLastModified` |Data e hora em que o alerta foi alterado pela última vez. |
| `TimeRaised` |Data e hora que o alerta foi gerado. |
| `TimeResolved` |Data e hora em que o alerta foi resolvido. Vazio se o alerta ainda não tiver sido resolvido. |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela a seguir fornece pesquisas de registo de amostras para registos de alerta recolhidos por esta solução: 

| Consulta | Descrição |
|:---|:---|
| Alerta &#124; onde SourceSystem == "OpsManager" e AlertSeverity == "error" e TimeRaised > (24h) |Alertas críticos levantados nas últimas 24 horas |
| Alerta &#124; onde alertSeverity == "warning" e TimeRaised > ago (24h) |Alertas de alerta levantados nas últimas 24 horas |
| Alerta &#124; onde SourceSystem == "OpsManager" e AlertState != "Fechado" e TimeRaised > (24h) &#124; resumir o Conde = contagem() por SourceDisplayName |Fontes com alertas ativos levantadas nas últimas 24 horas |
| Alerta &#124; onde SourceSystem == "OpsManager" e AlertSeverity == "erro" e TimeRaised > (24h) e AlertState != "Fechado" |Alertas críticos levantados nas últimas 24 horas que ainda estão ativos |
| Alerta &#124; onde SourceSystem == "OpsManager" e TimeRaised > (24h) e AlertState == "Fechado" |Alertas levantados nas últimas 24 horas que estão agora fechadas |
| Alerta &#124; onde SourceSystem == "OpsManager" e TimeRaised > (1d) &#124; resumir o Conde = contagem() por AlertaSeverity |Alertas levantados durante o último dia agrupados pela sua gravidade |
| Alerta &#124; onde o SourceSystem == "OpsManager" e TimeRaised > (1d) &#124; ordenar por RepeatCount desc |Alertas levantados durante o último dia classificados pelo seu valor de contagem repetida |



## <a name="next-steps"></a>Passos seguintes
* Veja o artigo [Alerts in Log Analytics](./alerts-overview.md) (Alertas no Log Analytics) para obter detalhes sobre a geração de alertas do Log Analytics.