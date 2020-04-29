---
title: 'Tutorial: Começar com consultas de Log Analytics'
description: Aprenda com este tutorial como escrever e gerir consultas de registo do Monitor Azure utilizando log Analytics no portal Azure.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80055504"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Tutorial: Começar com consultas de Log Analytics

Este tutorial mostra-lhe como usar o Log Analytics para escrever, executar e gerir consultas de registo do Azure Monitor no portal Azure. Pode utilizar consultas de Log Analytics para procurar termos, identificar tendências, analisar padrões e fornecer muitas outras informações a partir dos seus dados. 

Neste tutorial, aprende-se a utilizar o Log Analytics para:

> [!div class="checklist"]
> * Compreender o esquema de dados de registo
> * Escreva e faça consultas simples, e modifique o intervalo de tempo para consultas
> * Resultados de filtragem, classificação e consulta de grupo
> * Ver, modificar e partilhar visuais dos resultados da consulta
> * Guardar, carregar, exportar e copiar consultas e resultados

Para mais informações sobre consultas de registo, consulte a [visão geral das consultas de registo no Monitor Azure](log-query-overview.md).<br/>
Para um tutorial detalhado sobre a escrita de consultas de registo, consulte [Começar com consultas de log no Monitor Azure](get-started-queries.md).

## <a name="open-log-analytics"></a>Abrir log analytics
Para utilizar o Log Analytics, tem de ser inscrito numa conta Azure. Se não tiver uma conta Azure, [crie uma de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

Para completar a maioria dos passos deste tutorial, pode utilizar este ambiente de [demonstração,](https://portal.loganalytics.io/demo)que inclui muitos dados da amostra. Com o ambiente de demonstração, não será capaz de guardar consultas ou pin resultados num dashboard.

Também pode utilizar o seu próprio ambiente, se estiver a utilizar o Monitor Azure para recolher dados de registo em pelo menos um recurso Azure. Para abrir um espaço de trabalho de Log Analytics, no seu Monitor Azure navegação esquerda, selecione **Registos**. 

## <a name="understand-the-schema"></a>Compreender o esquema
Um *esquema* é uma coleção de mesas agruparadas em categorias lógicas. O esquema de demo tem várias categorias de soluções de monitorização. Por exemplo, a categoria **LogManagement** contém eventos Windows e Syslog, dados de desempenho e batimentos cardíacos do agente.

As tabelas de esquemas aparecem no separador **Tabelas** do espaço de trabalho Log Analytics. As tabelas contêm colunas, cada uma com um tipo de dados mostrado pelo ícone ao lado do nome da coluna. Por exemplo, a tabela **Evento** contém colunas de texto como **Computador** e colunas numéricas como **a Categoria de Eventos**.

![Esquema](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Escrever e executar consultas básicas

Log Analytics abre com uma nova consulta em branco no editor da **Consulta.**

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Escreva uma consulta
As consultas de registo do Azure Monitor utilizam uma versão da linguagem de consulta Kusto. As consultas podem começar com um nome de mesa ou um comando de [pesquisa.](/azure/kusto/query/searchoperator) 

A seguinte consulta recupera todos os registos da tabela **evento:**

```Kusto
Event
```

O tubo (/) caracteres separa os comandos, pelo que a saída do primeiro comando é a entrada do comando seguinte. Pode adicionar qualquer número de comandos a uma única consulta. A seguinte consulta recupera os registos da tabela **do Evento** e, em seguida, procura-os pelo **erro** de prazo em qualquer propriedade:

```Kusto
Event 
| search "error"
```

Uma única rutura de linha torna as consultas mais fáceis de ler. Mais de uma linha de rutura divide a consulta em consultas separadas.

Outra forma de escrever a mesma consulta é:

```Kusto
search in (Event) "error"
```

No segundo exemplo, o comando de **pesquisa** pesquisa apenas registos na tabela **Eventos** para o **erro**de termo .

Por padrão, o Log Analytics limita as consultas a um intervalo de tempo das últimas 24 horas. Para definir um intervalo de tempo diferente, pode adicionar um filtro **timeGenerated** explícito à consulta ou utilizar o controlo da **gama time.**

### <a name="use-the-time-range-control"></a>Utilize o controlo da gama tempo
Para utilizar o controlo da **gama time,** selecione-o na barra superior e, em seguida, selecione um valor da lista de dropdown, ou selecione **Custom** para criar uma gama de tempo personalizada.

![Selecionador de tempo](media/get-started-portal/time-picker.png)

- Os valores de intervalo de tempo estão em UTC, que podem ser diferentes do seu fuso horário local.
- Se a consulta definir explicitamente um filtro para **TimeGenerated**, o controlo do apanhador de tempo mostra **definido em consulta**, e é desativado para evitar um conflito.

### <a name="run-a-query"></a>Executar uma consulta
Para fazer uma consulta, coloque o cursor algures dentro da consulta e selecione **Corra** na barra superior ou prima **Shift**+**Enter**. A consulta funciona até encontrar uma linha em branco.

## <a name="filter-results"></a>Filtrar os resultados
Log Analytics limita os resultados a um máximo de 10.000 registos. Uma consulta geral `Event` como retorna a muitos resultados para ser útil. Pode filtrar os resultados da consulta através da limitação dos elementos da tabela na consulta, ou adicionando explicitamente um filtro aos resultados. A filtragem através dos elementos da tabela devolve um novo conjunto de resultados, enquanto um filtro explícito se aplica ao conjunto de resultados existente.

### <a name="filter-by-restricting-table-elements"></a>Filtrar restringindo elementos de mesa
Para `Event` filtrar os resultados da consulta aos eventos **de erro,** restringindo elementos de tabela na consulta:

1. Nos resultados da consulta, selecione a seta de dropdown ao lado de qualquer registo que tenha **Erro** na coluna **EventLevelName.** 
   
1. Nos detalhes expandidos, sobrepite e selecione o **...** ao lado do **EventLevelName**, e, em seguida, selecione **Incluir "Error"**. 
   
   ![Adicione filtro à consulta](media/get-started-portal/add-filter.png)
   
1. Note que a consulta no editor de **consulta** mudou agora para:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Selecione **Executar** para executar a nova consulta.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrar filtrando explicitamente os resultados
Para filtrar os resultados da `Event` consulta aos erros, filtrando os resultados da consulta: **Error**

1. Nos resultados da consulta, selecione o ícone **Filtro** ao lado da rubrica de coluna **EventLevelName**. 
   
1. No primeiro campo da janela pop-up, selecione **É igual a**, e no campo seguinte, introduza *erro*. 
   
1. Selecione **Filtro**.
   
   ![Filtro](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Ordenar, separar e selecionar colunas
Para classificar os resultados da consulta por uma coluna específica, como **timeGenerated [UTC],** selecione a rubrica da coluna. Selecione novamente a cabeça para alternar entre a ordem ascendente e descendente.

![Ordenar coluna](media/get-started-portal/sort-column.png)

Outra forma de organizar resultados é por grupos. Para agrupar os resultados por uma coluna específica, arraste o cabeçalho da coluna para a barra acima da tabela de resultados rotulada, **arraste um cabeçalho de coluna e deixe-o aqui para agrupar por essa coluna**. Para criar subgrupos, arraste outras colunas para a barra superior. Você pode reorganizar a hierarquia e a triagem dos grupos e subgrupos no bar.

![Grupos](media/get-started-portal/groups.png)

Para ocultar ou mostrar colunas nos resultados, selecione **Colunas** acima da tabela e, em seguida, selecione ou desmarque as colunas que deseja da lista de abandono.

![Selecionar colunas](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Ver e modificar gráficos
Também pode ver resultados de consulta em formatos visuais. Insira a seguinte consulta como exemplo:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Por defeito, os resultados aparecem numa tabela. Selecione **Gráfico** acima da tabela para ver os resultados numa vista gráfica.

![Gráfico de barras](media/get-started-portal/bar-chart.png)

Os resultados aparecem num gráfico de barras empilhado. Selecione outras opções como **Coluna Empilhada** ou **Pie** para mostrar outras opiniões dos resultados.

![Gráfico circular](media/get-started-portal/pie-chart.png)

Pode alterar propriedades da vista, tais como eixos x e y, ou preferências de agrupamento e divisão, manualmente a partir da barra de controlo.

Também pode definir a visão preferida na própria consulta, utilizando o operador de [renderização.](/azure/kusto/query/renderoperator)

## <a name="pin-results-to-a-dashboard"></a>Pin resultados para um dashboard
Para fixar uma tabela de resultados ou gráfico do Log Analytics para um painel azure partilhado, selecione **Pin para dashboard** na barra superior. 

![Afixar ao dashboard](media/get-started-portal/pin-dashboard.png)

No **Pin o outro** painel do painel de instrumentos, selecione ou crie um painel partilhado para fixar e selecione **Aplicar**. A tabela ou gráfico aparece no painel de instrumentos azure selecionado.

![Gráfico fixado ao painel de instrumentos](media/get-started-portal/pin-dashboard2.png)

Uma tabela ou gráfico que você pinum a um painel partilhado tem as seguintes simplificações: 

- Os dados estão limitados aos últimos 14 dias.
- Uma mesa mostra apenas até quatro colunas e as sete primeiras linhas.
- Gráficos com muitas categorias discretas automaticamente agrupam categorias menos povoadas em um **único** caixote do lixo.

## <a name="save-load-or-export-queries"></a>Guardar, carregar ou exportar consultas
Uma vez que cria uma consulta, pode guardar ou partilhar a consulta ou os resultados com outros. 

### <a name="save-queries"></a>Guardar consultas
Para salvar uma consulta:

1. Selecione **Guardar** na barra superior.
   
1. No diálogo **Save,** dê à consulta um **Nome**, utilizando os caracteres a-z, A-Z, 0-9, espaço, hífen, sublinhado, período, parênteses ou tubo. 
   
1. Selecione se deve guardar a consulta como **consulta** ou **função**. Funções são consultas que outras consultas podem referir. 
   
   Para guardar uma consulta em função, forneça uma **Função Alias,** que é um nome curto para outras consultas para chamar a esta consulta.
   
1. Forneça uma **categoria** para que o explorador de **consultas** utilize para a consulta.
   
1. Selecione **Guardar**.
   
   ![Salvar a função](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Consultas de carga
Para carregar uma consulta guardada, selecione **Query explorer** na parte superior direita. O painel do **explorador de Consulta** abre, enumerando todas as consultas por categoria. Expanda as categorias ou introduza um nome de consulta na barra de pesquisa e, em seguida, selecione uma consulta para carregá-la no editor da **Consulta**. Pode marcar uma consulta como **Favorita** selecionando a estrela ao lado do nome da consulta.

![Explorador de consulta](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Consultas de exportação e partilha
Para exportar uma consulta, selecione **Exportação** na barra superior e, em seguida, selecione **Exportação para CSV - todas as colunas,** **Exportação para CSV - colunas exibidas,** ou **Exportação para Power BI (consulta M)** da lista de abandono.

Para partilhar um link para uma consulta, selecione **link Copiar** na barra superior e, em seguida, selecione link Copy **para consulta,** **Copiar texto**de consulta ou **copiar resultados** de consulta para copiar para a área de cópia. Pode enviar o link de consulta a outros que tenham acesso ao mesmo espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo tutorial para saber mais sobre a escrita de consultas de registo do Monitor Azure.
> [!div class="nextstepaction"]
> [Escreva consultas de registo do Monitor Azure](get-started-queries.md)
