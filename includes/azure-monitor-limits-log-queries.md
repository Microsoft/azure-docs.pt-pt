---
title: incluir ficheiro
description: incluir ficheiro
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 5f2b77c7d8e1a2da9517183043231b717b6cceab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734078"
---
### <a name="general-query-limits"></a>Limites gerais de consulta

| Limite | Description |
|:---|:---|
| Linguagem da consulta | O Azure Monitor utiliza a mesma [linguagem de consulta kusto que](/azure/kusto/query/) o Azure Data Explorer. Consulte [as diferenças linguísticas de consulta de registo do Azure Monitor](/azure/data-explorer/kusto/query/) para elementos linguísticos KQL não suportados no Azure Monitor. |
| Regiões do Azure | As consultas de registo podem experimentar sobrecargas excessivas quando os dados abrangem espaços de trabalho log analytics em várias regiões do Azure. Consulte [os limites de consulta](../articles/azure-monitor/logs/scope.md#query-scope-limits) para mais detalhes. |
| Consultas entre recursos | Número máximo de recursos de Insights de Aplicação e espaços de trabalho log analytics numa única consulta limitada a 100.<br>A consulta de recursos cruzados não é suportada no View Designer.<br>A consulta de recursos cruzados nos alertas de registo é suportada na nova API agendada deQueryRules.<br>Consulte [os limites de consulta de recursos cruzados](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits) para obter mais detalhes. |

### <a name="user-query-throttling"></a>Estrangulamento da consulta do utilizador
O Azure Monitor tem vários limites de estrangulamento para proteger contra os utilizadores que enviam um número excessivo de consultas. Tal comportamento pode potencialmente sobrecarregar os recursos de backend do sistema e comprometer a capacidade de resposta do serviço. Os seguintes limites destinam-se a proteger os clientes de interrupções e a garantir um nível de serviço consistente. O estrangulamento do utilizador e os limites são concebidos para impactar apenas cenários de utilização extremas e não devem ser relevantes para o uso típico.


| Medir | Limite por utilizador | Description |
|:---|:---|:---|
| Consultas simultâneas | 5 | Se já existem 5 consultas em execução para o utilizador, quaisquer novas consultas são colocadas numa fila de conuncy por utilizador. Quando uma das consultas de execução terminar, a próxima consulta será retirada da fila e iniciada. Isto não inclui consultas de regras de alerta.
| Tempo na fila da concurrency | 3 minutos | Se uma consulta se sentar na fila por mais de 3 minutos sem ser iniciada, será terminada com uma resposta de erro HTTP com o código 429. |
| Total de consultas na fila da concurrency | 200 | Uma vez que o número de consultas na fila atinge 200, quaisquer consultas adicionais serão rejeitadas com um código de erro HTTP 429. Este número é além das 5 consultas que podem ser em execução simultaneamente. |
| Taxa de consultas | 200 consultas por 30 segundos | Esta é a taxa global que as consultas podem ser submetidas por um único utilizador a todos os espaços de trabalho.  Este limite aplica-se a consultas programáticas ou consultas iniciadas por peças de visualização como dashboards Azure e a página de resumo do espaço de trabalho Log Analytics. |

- Otimize as suas consultas como descrito nas [consultas de registo otimizado no Azure Monitor](../articles/azure-monitor/logs/query-optimization.md).
- Os dashboards e os livros de trabalho podem conter múltiplas consultas numa única vista que geram uma explosão de consultas sempre que carregam ou se atualizam. Considere dividi-los em múltiplas visões que carregam a pedido. 
- No Power BI, considere extrair apenas resultados agregados em vez de troncos crus.