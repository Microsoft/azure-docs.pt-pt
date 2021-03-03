---
title: Consultas de registo no Azure Monitor
description: Informações de referência para a língua de consulta kusto utilizada pelo Azure Monitor. Inclui elementos adicionais específicos do Azure Monitor e elementos não suportados em consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 7ce73fc69ac0ff88c6048aad39fd07ae802b978d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704230"
---
# <a name="log-queries-in-azure-monitor"></a>Consultas de registo no Azure Monitor
Os Registos do Monitor Azure são baseados no Azure Data Explorer, e as consultas de registo são escritas usando a mesma língua de consulta Kusto (KQL). Esta é uma língua rica projetada para ser fácil de ler e autor, por isso você deve ser capaz de começar a escrever consultas com alguma orientação básica.

As áreas em Azure Monitor onde utilizará consultas incluem:

- [Log Analytics](../logs/log-analytics-overview.md). Ferramenta primária no portal Azure para editar consultas de log e analisar interativamente os seus resultados. Mesmo que pretenda utilizar uma consulta de registo em outro lugar no Azure Monitor, normalmente irá escrevê-lo e testá-lo em Log Analytics antes de copiá-lo para a sua localização final.
- [Regras de alerta de registo](../alerts/alerts-overview.md). Identifique proativamente problemas a partir de dados no seu espaço de trabalho.  Cada regra de alerta baseia-se numa consulta de registo que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se deve ser criado um alerta.
- [Livros de trabalho.](../visualize/workbooks-overview.md) Inclua os resultados de consultas de registo usando diferentes visualizações em relatórios visuais interativos no portal Azure.
- [Paineles Azure](../visualize/tutorial-logs-dashboards.md). Fixar os resultados de qualquer consulta num dashboard Azure que lhe permita visualizar dados de registo e métrica em conjunto e partilhar opcionalmente com outros utilizadores do Azure.
- [Aplicativos lógicos](../logs/logicapp-flow-connector.md).  Utilize os resultados de uma consulta de registo num fluxo de trabalho automatizado utilizando Aplicações Lógicas.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). Utilize os resultados de uma consulta de registo num script PowerShell a partir de uma linha de comando ou de um manual da Azure Automation que utiliza o Get-AzOperationalInsightsSearchResults.
- [Azure Monitor Logs API](https://dev.loganalytics.io). Recupere os dados de registo do espaço de trabalho de qualquer cliente da API REST.  O pedido da API inclui uma consulta que é executada contra o Azure Monitor para determinar os dados a recuperar.

## <a name="getting-started"></a>Introdução
A melhor maneira de começar a aprender a escrever consultas de log usando KQL é alavancar tutoriais e amostras disponíveis.

- [Log Analytics tutorial](./log-analytics-tutorial.md) - Tutorial sobre a utilização das funcionalidades do Log Analytics, que é a ferramenta que utilizará no portal Azure para editar e executar consultas. Também permite escrever consultas simples sem trabalhar diretamente com a linguagem de consulta. Se nunca usou o Log Analytics antes, comece aqui para que compreenda a ferramenta que vai usar com os outros tutoriais e amostras.
- [Tutorial KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) - Caminhada guiada através de conceitos básicos de KQL e operadores comuns. Este é o melhor lugar para começar a se atualizar com a própria linguagem e a estrutura das consultas de log. 
- [Consultas de exemplo](../logs/example-queries.md) - Descrição das consultas de exemplo disponíveis no Log Analytics. Pode utilizar as consultas sem modificação ou usá-las como amostras para aprender KQL.
- [Amostras de consultas](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) - Consultas de amostras que ilustram uma variedade de conceitos diferentes.



## <a name="reference-documentation"></a>Documentação de referência
[A documentação para o KQL,](/azure/data-explorer/kusto/query/) incluindo a referência para todos os comandos e operadores, está disponível na documentação do Azure Data Explorer. Mesmo quando se obtém um recurso proficiente usando o KQL, ainda utilizará regularmente a referência para investigar novos comandos e cenários que nunca usou antes.


## <a name="language-differences"></a>Diferenças de idioma
Enquanto o Azure Monitor usa o mesmo KQL que o Azure Data Explorer, existem algumas diferenças. A documentação do KQL especificará os operadores que não são suportados pelo Azure Monitor ou que têm uma funcionalidade diferente. Os operadores específicos do Azure Monitor estão documentados no conteúdo do Azure Monitor. As secções seguintes fornecem uma lista das diferenças entre versões do idioma para referência rápida.

### <a name="statements-not-supported-in-azure-monitor"></a>Declarações não apoiadas no Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parâmetros de consultas](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções não suportadas no Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores não apoiados no Azure Monitor

* [União cross-cluster](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plugins não suportados no Azure Monitor

* [Plugin python](/azure/kusto/query/pythonplugin)
* [plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os seguintes operadores suportam funcionalidades específicas do Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [app()](../logs/app-expression.md)
* [recurso()](./resource-expression.md)
* [espaço de trabalho()](../logs/workspace-expression.md)

## <a name="next-steps"></a>Passos seguintes
- Caminhe por um [tutorial sobre consultas de escrita.](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)
- Aceda à documentação completa [de referência para a língua de consulta de Kusto.](/azure/kusto/query/)