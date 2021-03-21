---
title: Tutorial do Log Analytics
description: Aprenda com este tutorial como utilizar funcionalidades do Log Analytics no Azure Monitor para construir uma consulta de registo e analisar os seus resultados no portal Azure.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: 06a73b495cefc361db88d80413f4f4be50e105d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041149"
---
# <a name="log-analytics-tutorial"></a>Tutorial do Log Analytics
O Log Analytics é uma ferramenta no portal Azure para editar e executar consultas de registo a partir de dados recolhidos pelos Registos do Monitor Azure e analisar interativamente os seus resultados. Pode utilizar consultas de Log Analytics para obter registos que correspondam a critérios específicos, identificar tendências, analisar padrões e fornecer uma variedade de insights nos seus dados. 

Este tutorial acompanha-o através da interface Log Analytics, começa com algumas consultas básicas e mostra-lhe como pode trabalhar com os resultados. Aprenderá o seguinte:

> [!div class="checklist"]
> * Compreenda o esquema de dados de registo
> * Escreva e execute consultas simples, e modifique o intervalo de tempo para consultas
> * Filtrar, classificar e resultados de consulta de grupo
> * Ver, modificar e partilhar visuais dos resultados da consulta
> * Carga, exportação e consultas de cópia e resultados

> [!IMPORTANT]
> Este tutorial utiliza funcionalidades do Log Analytics para construir e executar uma consulta em vez de trabalhar com a própria consulta. Você vai aproveitar as funcionalidades do Log Analytics para construir uma consulta e usar outra consulta de exemplo. Quando estiver pronto para aprender a sintaxe das consultas e começar a editar diretamente a própria consulta, passe pelo tutorial de [Língua De Consulta de Kusto.](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) Esse tutorial percorre várias consultas de exemplo que pode editar e executar no Log Analytics, aproveitando várias das funcionalidades que vai aprender neste tutorial.


## <a name="prerequisites"></a>Pré-requisitos
Este tutorial utiliza o [ambiente de demonstração do Log Analytics,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)que inclui muitos dados de amostras que suportam as consultas de amostra. Também pode utilizar a sua própria subscrição Azure, mas pode não ter dados nas mesmas tabelas.

## <a name="open-log-analytics"></a>Abrir analítica de registo
Abra o [ambiente de demonstração do Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) ou selecione **Logs** do menu Azure Monitor na sua subscrição. Isto definirá o âmbito inicial para um espaço de trabalho Log Analytics, o que significa que a sua consulta irá selecionar a partir de todos os dados desse espaço de trabalho. Se selecionar **Logs** a partir do menu de um recurso Azure, o âmbito é definido apenas para registos a partir desse recurso. Consulte [o âmbito de consulta de registo](./scope.md) para obter mais detalhes sobre o âmbito de aplicação.

Pode ver o âmbito no canto superior esquerdo do ecrã. Se estiver a usar o seu próprio ambiente, verá uma opção para selecionar um âmbito diferente, mas esta opção não está disponível no ambiente de demonstração.

[![Âmbito de consulta](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Esquema de mesa
O lado esquerdo do ecrã inclui o separador **Tabelas** que lhe permite inspecionar as tabelas disponíveis no âmbito atual. Estes são agrupados por **solução** por padrão, mas altera-os em agrupamento ou filtra-os. 

Expanda a solução **de Gestão de Registos** e localize a tabela **AzureActivity.** Pode expandir a tabela para ver o seu esquema, ou pairar sobre o seu nome para mostrar informações adicionais sobre o mesmo. 

[![Vista de tabelas](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Clique **em Saber mais** para ir à referência de tabela que documenta cada tabela e as suas colunas. Clique em **Pré-visualizar dados** para ver rapidamente alguns registos recentes na tabela. Isto pode ser útil para garantir que estes são os dados que você espera antes de realmente fazer uma consulta com ele.

[![Dados de exemplo](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Escrever uma consulta
Vamos escrever uma consulta usando a tabela **AzureActivity.** Clique duas vezes no seu nome para adicioná-lo à janela de consulta. Também pode escrever diretamente na janela e até obter intellisense que ajudará a completar os nomes das tabelas no âmbito atual e comandos KQL.

Esta é a consulta mais simples que podemos escrever. Devolve todos os registos numa mesa. Executá-lo clicando no botão **Executar** ou premindo Shift+Enter com o cursor posicionado em qualquer lugar do texto de consulta.

[![Resultados da consulta](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Pode ver que temos resultados. O número de registos devolvidos pela consulta é apresentado no canto inferior direito. 

## <a name="filter"></a>Filtro

Vamos adicionar um filtro à consulta para reduzir o número de registos que são devolvidos. Selecione o **separador Filtro** no painel esquerdo. Isto mostra diferentes colunas nos resultados da consulta que pode utilizar para filtrar os resultados. Os valores de topo nessas colunas são apresentados com o número de registos com este valor. Clique em **Administrativo** em **CategoriaValue** e, em seguida, **Aplique & Run**. 

[![Painel de consulta](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

Um **onde** a declaração é adicionada à consulta com o valor que selecionou. Os resultados agora incluem apenas os registos com esse valor para que possa ver que a contagem de registos é reduzida.

[![Resultados de consulta filtrados](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Intervalo de tempo
Todas as tabelas num espaço de trabalho log analytics têm uma coluna chamada **TimeGenerated,** que é a altura em que o disco foi criado. Todas as consultas têm um intervalo de tempo que limita os resultados a registos com um valor **TimeGenerated** dentro desse intervalo. O intervalo de tempo pode ser definido na consulta ou com o seletor na parte superior do ecrã.

Por predefinição, a consulta devolverá os registos nas últimas 24 horas. Selecione o intervalo de **tempo** e altere-o para **7 dias**. Clique em **Executar** novamente para devolver os resultados. Pode ver que os resultados são devolvidos, mas temos uma mensagem aqui que não estamos a ver todos os resultados. Isto porque o Log Analytics pode devolver um máximo de 10.000 registos, e a nossa consulta devolveu mais registos do que isso. 

[![Intervalo de tempo](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Múltiplas condições de consulta
Vamos reduzir ainda mais os nossos resultados adicionando outra condição de filtro. Uma consulta pode incluir qualquer número de filtros para direcionar exatamente o conjunto de registos que deseja. Selecione **Sucesso** em **ActivityStatusValue** e clique **em Aplicar & Executar**. 

[![Consulta resulta de vários filtros](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Analisar resultados
Além de ajudá-lo a escrever e executar consultas, o Log Analytics fornece funcionalidades para trabalhar com os resultados. Comece por expandir um registo para ver os valores de todas as suas colunas.

[![Expandir recorde](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Clique no nome de qualquer coluna para classificar os resultados por esta coluna. Clique no ícone do filtro ao lado para fornecer uma condição de filtro. Isto é semelhante ao adicionar uma condição de filtro à própria consulta, exceto que este filtro é limpo se a consulta for executada novamente. Utilize este método se quiser analisar rapidamente um conjunto de registos como parte da análise interativa.

Por exemplo, coloque um filtro na coluna **CallerIpAddress** para limitar os registos a um único chamador. 

[![Filtro de resultados de consulta](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

Em vez de filtrar os resultados, pode agrupar os registos por uma determinada coluna. Limpe o filtro que acabou de criar e, em seguida, ligue o slider das **colunas do Grupo.** 

[![Colunas de grupo](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Agora arraste a coluna **CallerIpAddress** para a fila de agrupamento. Os resultados são agora organizados por essa coluna, e podes colapsar cada grupo para te ajudar na tua análise.

[![Resultados de consulta agrupados](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Trabalhar com gráficos
Vamos dar uma olhada numa consulta que usa dados numéricos que podemos ver num gráfico. Em vez de construir uma consulta, vamos selecionar uma consulta de exemplo.

Clique em **Consultas** no painel esquerdo. Este painel inclui consultas de exemplo que pode adicionar à janela de consulta. Se estiver a usar o seu próprio espaço de trabalho, deve ter uma variedade de consultas em várias categorias, mas se estiver a usar o ambiente de demonstração, só poderá ver uma única categoria de **espaços de trabalho do Log Analytics.** Expanda-o para ver as consultas na categoria.

Clique na consulta chamada **Request Count by ResponseCode**. Isto irá adicionar a consulta à janela de consulta. Note que a nova consulta é separada da outra por uma linha em branco. Uma consulta no KQL termina quando encontra uma linha em branco, de modo que estas são vistas como consultas separadas. 

[![Nova consulta](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

A consulta atual é a que o cursor está posicionado. Pode ver que a primeira consulta é realçada indicando que é a consulta atual. Clique em qualquer lugar da nova consulta para selecioná-lo e, em seguida, clique no botão **Executar** para executá-lo.

[![Gráfico de resultados de consulta](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Note que esta saída é um gráfico em vez de uma tabela como a última consulta. Isso é porque a consulta de exemplo usa um comando [de renderização](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) no final. Note que existem várias opções para trabalhar com o gráfico, como mudá-lo para outro tipo.

Tente selecionar **Resultados** para ver a saída da consulta como uma tabela. 

[![Tabela de resultados de consulta](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Passos seguintes

Agora que sabe como usar o Log Analytics, complete o tutorial sobre a utilização de consultas de registo.
> [!div class="nextstepaction"]
> [Escreva consultas de log monitor Azure](get-started-queries.md)