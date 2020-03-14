---
title: Recolher e analisar registos do Windows Event no Monitor Azure  Microsoft Docs
description: Descreve como configurar a recolha de registos do Windows Event pelo Azure Monitor e detalhes dos registos que criam.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274687"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Windows registra fontes de dados no Monitor De Eventos do Windows no Monitor Azure
Os registos do Windows Event são uma das fontes de dados mais [comuns](agent-data-sources.md) para a recolha de dados utilizando agentes do Windows, uma vez que muitas aplicações escrevem para o registo de eventos do Windows.  Pode recolher eventos a partir de registos padrão, como Sistema e Aplicação, além de especificar quaisquer registos personalizados criados por aplicações que precisa de monitorizar.

![Eventos do Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurar registos do Evento do Windows
Configure os registos do Evento do Windows no [menu Dados em Definições Avançadas](agent-data-sources.md#configuring-data-sources).

O Monitor Azure recolhe apenas eventos a partir dos registos de eventos do Windows que são especificados nas definições.  Pode adicionar um registo de eventodigitando em nome do registo e clicando **+** .  Para cada registo, apenas são recolhidos os eventos com as severidades selecionadas.  Verifique as severidades do registo em particular que pretende recolher.  Não é possível fornecer quaisquer critérios adicionais para filtrar eventos.

Ao escrever o nome de um registo de eventos, o Azure Monitor apresenta sugestões de nomes comuns de registos de eventos. Se o registo que pretende adicionar não aparecer na lista, ainda pode adicioná-lo digitando o nome completo do registo. Pode encontrar o nome completo do registo utilizando o espectador do evento. No espectador do evento, abra a página *Propriedades* para o registo e copie a cadeia do campo *Nome Completo.*

![Configurar eventos windows](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Os eventos críticos do registo de eventos do Windows terão uma gravidade de "Error" nos Registos do Monitor Azure.

## <a name="data-collection"></a>Recolha de dados
O Azure Monitor recolhe cada evento que corresponde a uma severidade selecionada a partir de um registo de eventos monitorizado à medida que o evento é criado.  O agente regista o seu lugar em cada registo de eventos de que recolhe.  Se o agente ficar offline por um período de tempo, então recolhe eventos de onde ficou parado, mesmo que esses eventos tenham sido criados enquanto o agente estava offline.  Existe um potencial para que estes eventos não sejam recolhidos se o registo do evento encerrar com eventos não recolhidos sendo substituídos enquanto o agente está offline.

>[!NOTE]
>O Azure Monitor não recolhe eventos de auditoria criados pelo SQL Server a partir da fonte *MSSQLSERVER* com id de evento 18453 que contém palavras-chave - *Sucesso Clássico* ou *Audite* e palavra-chave *0xa00000000000000000000000000000000 .*
>

## <a name="windows-event-records-properties"></a>Evento windows grava propriedades
Os registos de eventos windows têm um tipo de **Evento** e têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador de que o evento foi recolhido. |
| Categoria de Eventos |Categoria do evento. |
| EventData |Todos os dados do evento em formato bruto. |
| EventID |Número do evento. |
| EventLevel |Gravidade do evento de forma numérica. |
| EventLevelName |Gravidade do evento em forma de texto. |
| EventLog |Nome do registo do evento de que o evento foi recolhido. |
| ParâmetroXml |Valores de parâmetros de evento sinuoso em formato XML. |
| ManagementGroupName |Nome do grupo de gestão para agentes do Gestor de Operações do System Center.  Para outros agentes, este valor é `AOI-<workspace ID>` |
| RenderedDescription |Descrição do evento com valores de parâmetros |
| Origem |Fonte do evento. |
| SourceSystem |Tipo de agente do evento foi recolhido. <br> OpsManager – Agente Windows, quer ligue diretamente ou Gestor de Operações gerido <br> Linux - Todos os agentes linux  <br> AzureStorage – Diagnósticos Azure |
| TimeGenerated |Data e hora do evento foi criado no Windows. |
| UserName |Nome de utilizador da conta que registou o evento. |

## <a name="log-queries-with-windows-events"></a>Consultas de log com eventos windows
A tabela seguinte fornece diferentes exemplos de consultas de registo que recuperam registos do Windows Event.

| Consulta | Descrição |
|:---|:---|
| Evento |Todos os eventos do Windows. |
| Evento &#124; onde EventLevelName == "erro" |Todos os eventos windows com gravidade de erro. |
| Resumo &#124; do evento() por Fonte |Contagem de eventos windows por fonte. |
| Evento &#124; em que O Nome &#124; do Nível de Evento == "erro" resume a contagem() por Fonte |Contagem de erros do Windows por fonte. |


## <a name="next-steps"></a>Passos seguintes
* Configure o Log Analytics para recolher [outras fontes de dados](agent-data-sources.md) para análise.
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.  
* Configure [a recolha de contadores de desempenho](data-sources-performance-counters.md) dos seus agentes Windows.
