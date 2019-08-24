---
title: Coletar e analisar logs de eventos do Windows no Azure Monitor | Microsoft Docs
description: Descreve como configurar a coleta de logs de eventos do Windows por Azure Monitor e detalhes dos registros que eles criam.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: cc81a8d8023d0724f4ecb71c157e8f575aa9edc8
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997477"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Fontes de dados de log de eventos do Windows no Azure Monitor
Os logs de eventos do Windows são uma das [fontes de dados](agent-data-sources.md) mais comuns para coletar dados usando agentes do Windows, já que muitos aplicativos gravam no log de eventos do Windows.  Você pode coletar eventos de logs padrão, como sistema e aplicativo, além de especificar quaisquer logs personalizados criados por aplicativos que você precisa monitorar.

![Eventos do Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurando logs de eventos do Windows
Configure logs de eventos do Windows no [menu dados em configurações avançadas](agent-data-sources.md#configuring-data-sources).

Azure Monitor coleta apenas eventos dos logs de eventos do Windows especificados nas configurações.  Você pode adicionar um log de eventos digitando o nome do log e clicando **+** em.  Para cada log, somente os eventos com as severidades selecionadas são coletados.  Verifique as severidades para o log específico que você deseja coletar.  Você não pode fornecer nenhum critério adicional para filtrar eventos.

À medida que você digita o nome de um log de eventos, Azure Monitor fornece sugestões de nomes de log de eventos comuns. Se o log que você deseja adicionar não aparecer na lista, você ainda poderá adicioná-lo digitando o nome completo do log. Você pode encontrar o nome completo do log usando o Visualizador de eventos. No Visualizador de eventos, abra a página de *Propriedades* do log e copie a cadeia de caracteres do campo *nome completo* .

![Configurar eventos do Windows](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Os eventos críticos do log de eventos do Windows terão uma severidade de "erro" nos logs de Azure Monitor.

## <a name="data-collection"></a>Recolha de dados
Azure Monitor coleta cada evento que corresponde a uma severidade selecionada de um log de eventos monitorado conforme o evento é criado.  O agente registra seu local em cada log de eventos do qual ele coleta.  Se o agente ficar offline por um período de tempo, ele coletará eventos de onde ele parou por último, mesmo que esses eventos tenham sido criados enquanto o agente estava offline.  Há um potencial para esses eventos não serem coletados se o log de eventos encapsular com eventos não coletados sendo substituídos enquanto o agente estiver offline.

>[!NOTE]
>Azure Monitor não coleta eventos de auditoria criados por SQL Server da origem *MSSQLSERVER* com a ID de evento 18453 que contém palavras-chave- *clássico* ou *êxito de auditoria* e palavra-chave *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Propriedades de registros de eventos do Windows
Os registros de eventos do Windows têm um tipo de **evento** e têm as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Computer |Nome do computador do qual o evento foi coletado. |
| EventCategory |Categoria do evento. |
| EventData |Todos os dados de evento em formato bruto. |
| 1008 |Número do evento. |
| EventLevel |Severidade do evento em formato numérico. |
| EventLevelName |Severidade do evento na forma de texto. |
| EventLog |Nome do log de eventos do qual o evento foi coletado. |
| ParameterXml |Valores de parâmetro de evento em formato XML. |
| ManagementGroupName |Nome do grupo de gerenciamento para agentes de System Center Operations Manager.  Para outros agentes, esse valor é`AOI-<workspace ID>` |
| RenderedDescription |Descrição do evento com valores de parâmetro |
| Source |Origem do evento. |
| SourceSystem |Tipo de agente do qual o evento foi coletado. <br> OpsManager – agente do Windows, conexão direta ou Operations Manager gerenciado <br> Linux – todos os agentes do Linux  <br> AzureStorage – Diagnóstico do Azure |
| TimeGenerated |Data e hora em que o evento foi criado no Windows. |
| UserName |Nome de usuário da conta que registrou o evento. |

## <a name="log-queries-with-windows-events"></a>Consultas de log com eventos do Windows
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros de eventos do Windows.

| Consulta | Descrição |
|:---|:---|
| Evento |Todos os eventos do Windows. |
| Evento &#124; em que EventLevelName = = "Error" |Todos os eventos do Windows com severidade de erro. |
| Contagem &#124; de Resumo de eventos () por origem |Contagem de eventos do Windows por origem. |
| Evento &#124; em que EventLevelName = = "Error &#124; " resume Count () por origem |Contagem de eventos de erro do Windows por origem. |


## <a name="next-steps"></a>Passos Seguintes
* Configure Log Analytics para coletar outras [fontes de dados](agent-data-sources.md) para análise.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  
* Configure a [coleta de contadores de desempenho](data-sources-performance-counters.md) de seus agentes do Windows.
