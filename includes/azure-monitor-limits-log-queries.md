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
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82072642"
---
### <a name="general-query-limits"></a>Limites gerais de consulta

| Limite | Descrição |
|:---|:---|
| Linguagem da consulta | O Azure Monitor usa a mesma [linguagem de consulta Kusto](/azure/kusto/query/) que o Azure Data Explorer. Consulte [as diferenças linguísticas](../articles/azure-monitor/log-query/data-explorer-difference.md) de consulta de registo do Azure Monitor para elementos linguísticos KQL não suportados no Monitor Azure. |
| Regiões do Azure | As consultas de registo podem experimentar sobrecargas excessivas quando os dados abrangem espaços de trabalho do Log Analytics em várias regiões do Azure. Consulte [os limites](../articles/azure-monitor/log-query/scope.md#query-limits) de consulta para mais detalhes. |
| Consultas de recursos cruzados | Número máximo de recursos de Application Insights e espaços de trabalho log Analytics numa única consulta limitada a 100.<br>A consulta de recursos cruzados não é suportada no View Designer.<br>A consulta de recursos cruzados em alertas de registo é suportada na nova API agendada.<br>Consulte [os limites de consulta de recursos cruzados](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) para obter detalhes. |

### <a name="user-query-throttling"></a>Estrangulamento da consulta do utilizador
O Monitor Azure tem vários limites de estrangulamento para proteger contra os utilizadores que enviam um número excessivo de consultas. Tal comportamento pode potencialmente sobrecarregar os recursos de backend do sistema e comprometer a capacidade de resposta do serviço. Os seguintes limites destinam-se a proteger os clientes de interrupções e a garantir um nível de serviço consistente. O estrangulamento e os limites do utilizador são concebidos para impactar apenas um cenário de utilização extremo e não devem ser relevantes para a utilização típica.


| Medir | Limite por utilizador | Descrição |
|:---|:---|:---|
| Consultas simultâneas | 5 | Se já existem 5 consultas em execução para o utilizador, quaisquer novas consultas são colocadas numa fila de condivisões por utilizador. Quando uma das consultas de corrida terminar, a próxima consulta será retirada da fila e iniciada. Isto não inclui consultas de regras de alerta.
| Tempo na fila da moeda | 2,5 minutos | Se uma consulta se sentar na fila por mais de 2,5 minutos sem ser iniciada, será terminada com uma resposta de erro HTTP com o código 429. |
| Consultas totais na fila da moeda | 40 | Uma vez que o número de consultas na fila atinja 40, quaisquer consultas adicionais serão rejeitadas com um código de erro HTTP 429. Este número é além das 5 consultas que podem ser em funcionamento simultaneamente. |
| Taxa de consulta | 200 consultas por 30 segundos | Esta é a taxa global que as consultas podem ser submetidas por um único utilizador a todos os espaços de trabalho.  Este limite aplica-se a consultas programáticas ou consultas iniciadas por peças de visualização, como os dashboards Azure e a página de resumo do espaço de trabalho Log Analytics. |

- Otimize as suas consultas conforme descrito nas consultas de [registo otimizadas no Monitor Azure](../articles/azure-monitor/log-query/query-optimization.md).
- Dashboards e livros de trabalho podem conter múltiplas consultas numa única vista que geram uma explosão de consultas sempre que carregam ou atualização. Considere dividi-los em várias visões que carregam a pedido. 
- No Power BI, considere extrair apenas resultados agregados em vez de troncos brutos.