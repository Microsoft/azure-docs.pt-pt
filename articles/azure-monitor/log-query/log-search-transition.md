---
title: Visualização e análise de dados no Azure Log Analytics Microsoft Docs
description: Assistência para utilizadores de Log Analytics registar pesquisa para experiência de consulta de registo do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77670139"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Transição da pesquisa de log Analytics para registos do Monitor Azure
A pesquisa de registo no Log Analytics foi recentemente substituída por uma nova experiência para analisar registos do Azure Monitor. A página de pesquisa do Log ainda está acessível através do item do menu **Logs (clássico)** na página **de espaços de trabalho Do Log Analytics** no portal Azure, mas será removido a 15 de fevereiro de 2019. Este artigo descreve diferenças entre as duas experiências para ajudá-lo a transitar da pesquisa de registo. 

## <a name="filter-results-of-a-query"></a>Filtrar os resultados de uma consulta
Na Pesquisa de Registo, uma lista de filtros é apresentada à medida que os resultados da pesquisa são entregues. Selecione um filtro e clique **em Aplicar** para executar a consulta com o filtro selecionado.

![Filtro de pesquisa de registo](media/log-search-transition/filter-log-search.png)

Nos registos do Monitor Azure, selecione *Filter (pré-visualização)* para exibir filtros. Clique no ícone do filtro para exibir filtros de adição. Selecione um filtro e clique em **Aplicar & Executar** para executar a consulta com o filtro selecionado.

![Filtro de registos](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrair campos personalizados 
Na Pesquisa de Registo, extrai [campos personalizados](../platform/custom-fields.md) a partir da vista Lista, onde o menu de um campo inclui os _campos de extrato de_ação da Tabela.

![Log Search campos de extrato](media/log-search-transition/extract-fields-log-search.png)

Nos registos do Azure Monitor, extrai campos personalizados a partir da vista da mesa. Expanda um registo clicando na seta à sua esquerda e clique na elipse para aceder à ação dos _campos Extratos._

![Troncos extraem campos](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funções e grupos informáticos
Para guardar uma pesquisa em 'Registar', **selecione Pesquisas Guardadas** e **Adicione** para fornecer um nome, categoria e texto de consulta. Criar um [grupo de computador](../platform/computer-groups.md) adicionando um pseudónimo de função.

![Guardar pesquisa de registo](media/log-search-transition/save-search-log-search.png)

Para guardar a consulta atual nos registos do Azure Monitor, **selecione Save**. Alterar **Guardar quanto** à _função_ e fornecer um pseudónimo de **função** para criar uma [função](functions.md). _Selecione Guarde esta consulta como um grupo de computador_ para utilizar o pseudónimo de função para um grupo de [computador](../platform/computer-groups.md).

![Guardar consulta de registo](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Consultas salvas
Na Pesquisa de Registo, as suas consultas guardadas estão disponíveis através do item da barra de ação **Pesquisas guardadas.** Nos registos do Azure Monitor, aceda a consultas guardadas do [Explorador de Consultas](../log-query/get-started-portal.md#save-queries).

![Explorador de consultas](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Perfurar em linhas resumidas
Na Pesquisa de Registo, pode clicar numa linha numa consulta resumida para lançar outra consulta que lista registos detalhados nessa linha.

![Perfuração de pesquisa de registo](media/log-search-transition/drilldown-search.png)

Nos registos do Azure Monitor, tem de modificar a consulta para devolver estes registos. Expanda uma das linhas dos resultados e clique no **+** seguinte ao valor para adicioná-lo à consulta. Em seguida, comente o comando **resumir** e executar a consulta novamente.

![Perfuração de registos do Monitor Azure](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Tomar medidas
Na Pesquisa de Registo, pode [iniciar um livro de execução](take-action.md) a partir de um resultado de pesquisa selecionando **Ação**.

![Tomar medidas](media/log-search-transition/take-action-log-search.png)

Nos registos do Azure Monitor, [crie um alerta a partir da consulta de registo](../platform/alerts-log.md). Configurar um grupo de ação com uma ou mais ações que serão executadas em resposta ao alerta.

![Grupo de ações](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a nova experiência de [registos do Azure Monitor](get-started-portal.md).
