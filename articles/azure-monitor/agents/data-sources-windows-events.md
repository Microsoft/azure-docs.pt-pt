---
title: Recolher fontes de dados de registo de eventos do Windows com o agente Log Analytics no Azure Monitor
description: Descreve como configurar a recolha de registos do Evento Do Windows pelo Azure Monitor e detalhes dos registos que criam.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 0eaa73fa7e0a9896a875af7e3a3aab22db2a37d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657064"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Recolher fontes de dados de registo de eventos do Windows com o agente Log Analytics
Os registos do Windows Event são uma das fontes de dados mais [comuns](../agents/agent-data-sources.md) para agentes do Log Analytics em máquinas virtuais windows, uma vez que muitas aplicações escrevem no registo de eventos do Windows.  Pode recolher eventos a partir de registos padrão, como Sistema e Aplicação, além de especificar quaisquer registos personalizados criados pelas aplicações que precisa de monitorizar.

> [!IMPORTANT]
> Este artigo abrange a recolha de eventos do Windows com o [agente Log Analytics,](./log-analytics-agent.md) que é um dos agentes utilizados pelo Azure Monitor. Outros agentes recolhem dados diferentes e são configurados de forma diferente. Consulte [a visão geral dos agentes do Azure Monitor](../agents/agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que podem recolher.

![Eventos do Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurar registos de eventos do Windows
Configurar os registos do Evento do Windows a partir do menu de [configuração de Agentes](../agents/agent-data-sources.md#configuring-data-sources) para o espaço de trabalho Log Analytics.

O Azure Monitor recolhe apenas eventos a partir dos registos de eventos do Windows que são especificados nas definições.  Pode adicionar um registo de eventos digitando o nome do registo e clicando **+** .  Para cada registo, apenas são recolhidos os eventos com as severidades selecionadas.  Verifique as gravidades do registo específico que pretende recolher.  Não é possível fornecer critérios adicionais para filtrar eventos.

Ao escrever o nome de um registo de eventos, o Azure Monitor fornece sugestões de nomes comuns de registo de eventos. Se o registo que pretende adicionar não aparecer na lista, ainda pode adicioná-lo digitando o nome completo do registo. Pode encontrar o nome completo do registo utilizando o visualizador do evento. No espectador de eventos, abra a página *Propriedades* para o registo e copie a cadeia do campo *Nome Completo.*

[![Configurar eventos windows](media/data-sources-windows-events/configure.png)](media/data-sources-windows-events/configure.png#lightbox)

> [!IMPORTANT]
> Não se pode configurar a recolha de eventos de segurança do espaço de trabalho. Você deve usar [O Centro de Segurança Azure](../../security-center/security-center-enable-data-collection.md) ou [Azure Sentinel](../../sentinel/connect-windows-security-events.md) para recolher eventos de segurança.


> [!NOTE]
> Os eventos críticos do registo de eventos do Windows terão uma gravidade de "Erro" nos Registos do Monitor Azure.

## <a name="data-collection"></a>Recolha de dados
O Azure Monitor recolhe cada evento que corresponda a uma gravidade selecionada a partir de um registo de eventos monitorizado à medida que o evento é criado.  O agente grava o seu lugar em cada registo de evento que recolhe.  Se o agente ficar offline por um período de tempo, então recolhe eventos de onde ficou pela última vez, mesmo que esses eventos tenham sido criados enquanto o agente estava offline.  Existe um potencial para estes eventos não serem recolhidos se o registo do evento terminar com eventos não recolhidos sendo substituídos enquanto o agente está offline.

>[!NOTE]
>O Azure Monitor não recolhe eventos de auditoria criados pelo SQL Server a partir da fonte *MSSQLSERVER* com o ID 18453 do evento que contém palavras-chave - *Sucesso clássico* ou *de auditoria* e palavra-chave *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Evento do Windows regista propriedades
Os registos de eventos do Windows têm um tipo de **Evento** e têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador do evento de onde o evento foi recolhido. |
| EventCategoria |Categoria do evento. |
| EventData |Todos os dados do evento em formato bruto. |
| ID do Evento |Número do evento. |
| EventoLevel |Gravidade do evento em forma numérica. |
| EventoLevelName |Gravidade do evento em forma de texto. |
| EventLog |O nome do registo do evento de onde o evento foi recolhido. |
| ParâmetroXml |Valores de parâmetro de evento no formato XML. |
| ManagementGroupName |Nome do grupo de gestão dos agentes gestores de operações do System Center.  Para outros agentes, este valor é `AOI-<workspace ID>` |
| Descrição prestada |Descrição do evento com valores de parâmetros |
| Origem |Fonte do evento. |
| SourceSystem |Tipo de agente do evento foi recolhido. <br> OpsManager – Agente Windows, ou connect direto ou Gestor de Operações gerido <br> Linux - Todos os agentes Linux  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Data e hora o evento foi criado no Windows. |
| Nome de Utilizador |Nome de utilizador da conta que registou o evento. |

## <a name="log-queries-with-windows-events"></a>Consultas de registo com eventos do Windows
A tabela seguinte fornece diferentes exemplos de consultas de registo que recuperam registos do Windows Event.

| Consulta | Description |
|:---|:---|
| Evento |Todos os eventos do Windows. |
| Evento &#124; onde EventLevelName == "erro" |Todos os eventos do Windows com gravidade de erro. |
| Evento &#124; resumir a contagem() por Fonte |Contagem de eventos do Windows por fonte. |
| Evento &#124; onde EventLevelName == "erro" &#124; resumir a contagem() por Fonte |Contagem de erros do Windows por fonte. |


## <a name="next-steps"></a>Passos seguintes
* Configure o Log Analytics para recolher [outras fontes de dados](../agents/agent-data-sources.md) para análise.
* Saiba mais [sobre consultas de registo](../logs/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.  
* Configure [a recolha de contadores de desempenho dos](data-sources-performance-counters.md) seus agentes Windows.
