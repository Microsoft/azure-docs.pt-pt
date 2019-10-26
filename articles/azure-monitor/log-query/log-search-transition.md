---
title: Exibindo e analisando dados no Azure Log Analytics | Microsoft Docs
description: Assistência para usuários de Log Analytics pesquisa de log para Azure Monitor experiência de consulta de log.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: b738d532cb458506ca50a07dea11687040f99e02
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900333"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Transição de Log Analytics pesquisa de log para logs de Azure Monitor
A pesquisa de log no Log Analytics foi substituída recentemente por uma nova experiência para analisar os logs de Azure Monitor. A página pesquisa de logs ainda está acessível no momento por meio do item de menu **logs (clássico)** na página **log Analytics espaços de trabalho** no portal do Azure, mas será removida em 15 de fevereiro de 2019. Este artigo descreve as diferenças entre as duas experiências para ajudá-lo a fazer a transição da pesquisa de logs. 

## <a name="filter-results-of-a-query"></a>Filtrar os resultados de uma consulta
Na pesquisa de logs, uma lista de filtros é exibida à medida que os resultados da pesquisa são entregues. Selecione um filtro e clique em **aplicar** para executar a consulta com o filtro selecionado.

![Filtro de pesquisa de log](media/log-search-transition/filter-log-search.png)

Em logs de Azure Monitor, selecione *Filtrar (visualização)* para exibir filtros. Clique no ícone de filtro para exibir filtros de adição. Selecione um filtro e clique em **aplicar & executar** para executar a consulta com o filtro selecionado.

![Filtro de logs](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrair campos personalizados 
Na pesquisa de logs, você extrai [campos personalizados](../platform/custom-fields.md) da exibição de lista, onde o menu de um campo inclui a ação _extrair campos da tabela_.

![Campos de extração da pesquisa de logs](media/log-search-transition/extract-fields-log-search.png)

Nos logs de Azure Monitor, você extrai campos personalizados da exibição de tabela. Expanda um registro clicando na seta à esquerda e, em seguida, clique nas reticências para acessar a ação _extrair campos_ .

![Os logs extraem campos](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funções e grupos de computadores
Para salvar uma pesquisa na pesquisa de logs, selecione **pesquisas salvas** e **adicione** para fornecer um nome, uma categoria e um texto de consulta. Crie um [grupo de computadores](../platform/computer-groups.md) adicionando um alias de função.

![Salvar pesquisa de logs](media/log-search-transition/save-search-log-search.png)

Para salvar a consulta atual em logs de Azure Monitor, selecione **salvar**. Altere a _função_ **salvar como** para e forneça um **alias de função** para criar uma [função](functions.md). Selecione _salvar esta consulta como um grupo de computadores_ para usar o alias de função para um [grupo de computadores](../platform/computer-groups.md).

![Salvar consulta de log](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Consultas salvas
Na pesquisa de logs, suas consultas salvas estão disponíveis por meio das **pesquisas salvas**do item da barra de ações. Em logs de Azure Monitor, acesse consultas salvas no [Gerenciador de consultas](../log-query/get-started-portal.md#save-queries).

![Gerenciador de consultas](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Fazer drill down em linhas resumidas
Na pesquisa de logs, você pode clicar em uma linha em uma consulta resumida para iniciar outra consulta que lista os registros detalhados nessa linha.

![Busca detalhada de pesquisa de logs](media/log-search-transition/drilldown-search.png)

Nos logs de Azure Monitor, você deve modificar a consulta para retornar esses registros. Expanda uma das linhas nos resultados e clique no **+** ao lado do valor para adicioná-lo à consulta. Em seguida, comente o comando **resumir** e execute a consulta novamente.

![Busca detalhada de logs de Azure Monitor](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Executar ação
Na pesquisa de logs, você pode [Iniciar um runbook](take-action.md) de um resultado de pesquisa selecionando **executar ação**.

![Executar ação](media/log-search-transition/take-action-log-search.png)

Em logs de Azure Monitor, [crie um alerta da consulta de log](../platform/alerts-log.md). Configure um grupo de ação com uma ou mais ações que serão executadas em resposta ao alerta.

![Grupo de ação](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a nova [experiência de logs de Azure monitor](get-started-portal.md).