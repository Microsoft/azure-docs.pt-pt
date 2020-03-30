---
title: Visualização e análise de dados no Azure Log Analytics Microsoft Docs
description: Assistência para utilizadores de log analytics pesquisa para experiência de consulta de log Do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670139"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Transição da pesquisa de log Analytics para registos do Monitor Azure
A pesquisa de log no Log Analytics foi recentemente substituída por uma nova experiência para analisar os registos do Monitor Azure. A página de pesquisa de Registos ainda está acessível através do item do menu **Logs (clássico)** na página de espaços de **trabalho Log Analytics** no portal Azure, mas será removida a 15 de fevereiro de 2019. Este artigo descreve diferenças entre as duas experiências para ajudá-lo a transitar da pesquisa de registo. 

## <a name="filter-results-of-a-query"></a>Filtrar resultados de uma consulta
No Registo de Pesquisa, é apresentada uma lista de filtros à medida que os resultados da pesquisa são entregues. Selecione um filtro e clique **Em aplicar** a consulta com o filtro selecionado.

![Filtro de pesquisa de registo](media/log-search-transition/filter-log-search.png)

Nos registos do Monitor Azure, selecione *Filter (pré-visualização)* para visualizar filtros. Clique no ícone do filtro para exibir filtros de adição. Selecione um filtro e clique **Em aplicar & Executar** para executar a consulta com o filtro selecionado.

![Filtro de logs](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrair campos personalizados 
Em Pesquisa de Registo, extrai [campos personalizados](../platform/custom-fields.md) da vista Lista, onde o menu de um campo inclui os _campos de extrato de_ação da Tabela .

![Campos de extração de pesquisa de registo](media/log-search-transition/extract-fields-log-search.png)

Nos registos do Monitor Azure, extrai-se campos personalizados da vista para a mesa. Expanda um registo clicando na seta à sua esquerda e, em seguida, clique na elipse para aceder à ação dos _campos extrato._

![Campos de extração de troncos](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funções e grupos informáticos
Para guardar uma pesquisa em 'Pesquisa de Registos', selecione **Pesquisas Guardadas** e **Adicione** para fornecer um nome, categoria e texto de consulta. Crie um [grupo de computador](../platform/computer-groups.md) adicionando um pseudónimo de função.

![Salvar a pesquisa de registo](media/log-search-transition/save-search-log-search.png)

Para guardar a consulta atual nos registos do Monitor Azure, selecione **Save**. Altere **Guardar quanto** à _Função_ e forneça um pseudónimo de **função** para criar uma [função](functions.md). Selecione _Guarde esta consulta como um grupo de computador_ para utilizar o pseudónimo de função para um grupo de [computador](../platform/computer-groups.md).

![Guardar consulta de registo](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Consultas guardadas
Em Pesquisa de Registos, as suas consultas guardadas estão disponíveis através do item de barra de ação **Guardou pesquisas .** Nos registos do Monitor Azure, aceda a consultas guardadas pelo [Query Explorer](../log-query/get-started-portal.md#save-queries).

![Explorador de consulta](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Perfurar em linhas resumidas
Em 'Pesquisa de Registos', pode clicar numa linha numa consulta resumida para lançar outra consulta que lista registos detalhados nessa linha.

![Perfuração de pesquisa de log](media/log-search-transition/drilldown-search.png)

Nos registos do Monitor Azure, tem de modificar a consulta para devolver estes registos. Expanda uma das linhas nos resultados e clique na **+** próxima ao valor para adicioná-la à consulta. Em seguida, comentar o **comando resumir** e executar a consulta novamente.

![Perfuração de logs do Monitor Azure](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Tomar medidas
Em 'Pesquisa de Registos', pode iniciar um livro de [execução](take-action.md) a partir de um resultado de pesquisa selecionando **Tomar medidas**.

![Tomar medidas](media/log-search-transition/take-action-log-search.png)

Nos registos do Monitor Azure, [crie um alerta a partir da consulta de registo](../platform/alerts-log.md). Configure um grupo de ação com uma ou mais ações que irão funcionar em resposta ao alerta.

![Grupo de ações](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a nova experiência de [registos do Azure Monitor.](get-started-portal.md)
