---
title: Ver e analisar os dados no Azure Log Analytics | Documentos da Microsoft
description: Assistência para os utilizadores de pesquisa de registos do Log Analytics à experiência de consulta de registo do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519895"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Transição de pesquisa de registos do Log Analytics para registos do Azure Monitor
A pesquisa de registos no Log Analytics recentemente foi substituída por uma nova experiência para analisar os registos do Azure Monitor. A página de pesquisa de registo está atualmente continua acessível através do **registos (clássico)** item de menu no **áreas de trabalho do Log Analytics** página na Azure mas portal será removida até 15 de Fevereiro de 2019. Este artigo descreve as diferenças entre as duas experiências para o ajudar a fazer a transição de pesquisa de registos. 

## <a name="filter-results-of-a-query"></a>Filtrar os resultados de uma consulta
Na pesquisa de registos, uma lista dos filtros são apresentados como os resultados da pesquisa são entregues. Selecione um filtro e clique em **aplicar** para executar a consulta com o filtro selecionado.

![Filtro de pesquisa de registo](media/log-search-transition/filter-log-search.png)

Nos logs de Monitor do Azure, selecione *filtro (pré-visualização)* para apresentar os filtros. Clique no ícone de filtro para apresentar os filtros de adição. Selecione um filtro e clique em **aplicam-se e & xecutar** para executar a consulta com o filtro selecionado.

![Filtrar registos](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrair campos personalizados 
Na pesquisa de registos, extrair [campos personalizados](../platform/custom-fields.md) na vista de lista, em que o menu de um campo inclui a ação _extrair os campos da tabela_.

![Campos de extração de pesquisa de registo](media/log-search-transition/extract-fields-log-search.png)

Nos registos do Azure Monitor, é possível extrair campos personalizados de vista de tabela. Expanda um registo, clicando na seta à sua esquerda, em seguida, clique nas reticências para aceder a _extrair campos_ ação.

![Extrair os registos de campos](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>As funções e grupos de computadores
Para guardar uma pesquisa na pesquisa de registos, selecione **pesquisas guardadas** e **Add** para fornecer um nome, a categoria e o texto da consulta. Criar uma [grupo de computadores](../platform/computer-groups.md) ao adicionar um alias de função.

![Guardar a pesquisa de registos](media/log-search-transition/save-search-log-search.png)

Para guardar a consulta atual nos registos do Azure Monitor, selecione **guardar**. Alteração **guardar como** ao _função_ e fornecer uma **Alias de função** para criar um [função](functions.md). Selecione _guardar esta consulta como grupo de computadores_ para utilizar o alias de função para um [grupo de computadores](../platform/computer-groups.md).

![Guardar consulta de registo](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Consultas guardadas
Na pesquisa de registos, as consultas guardadas estão disponíveis através do item de barra de ação **pesquisas guardadas**. Nos registos do Azure Monitor, aceder a consultas guardadas partir [Explorador de consultas](../log-query/get-started-portal.md#save-queries).

![Explorador de consultas](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Faça uma busca detalhada nas linhas resumidas
Na pesquisa de registos, pode clicar numa linha numa consulta resumida para iniciar outra consulta, que apresenta uma lista detalhada de registos nessa linha.

![Detalhamento de pesquisa de registo](media/log-search-transition/drilldown-search.png)

Nos registos do Azure Monitor, tem de modificar a consulta para devolver estes registos. Expanda uma das linhas nos resultados e clique nas **+** junto ao valor para o adicionar à consulta. Em seguida, comente a **resumir** de comandos e execute novamente a consulta.

![Pesquisa de registos de Monitor do Azure](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Tomar medidas
Na pesquisa de registos, pode [iniciar um runbook](take-action.md) de um resultado de pesquisa selecionando **agir**.

![Tomar medidas](media/log-search-transition/take-action-log-search.png)

Nos registos do Azure Monitor, [criar um alerta a partir da consulta de registo](../platform/alerts-log.md). Configure um grupo de ação com uma ou mais ações que serão executados em resposta ao alerta.

![grupo de ação](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a nova [experiência de registos do Azure Monitor](get-started-portal.md).