---
title: 'Tutorial: Começar com consultas de Log Analytics'
description: Aprenda com este tutorial como escrever e gerir consultas de registo do Azure Monitor utilizando o Log Analytics no portal Azure.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: b337065f125d12e309dd1f7fcc56c2af72b1c28c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088362"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Tutorial: Começar com consultas de Log Analytics

Este tutorial mostra-lhe como usar o Log Analytics para escrever, executar e gerir consultas de registo do Azure Monitor no portal Azure. Pode utilizar consultas de Log Analytics para procurar termos, identificar tendências, analisar padrões e fornecer muitos outros insights a partir dos seus dados. 

Neste tutorial, aprende-se a usar o Log Analytics para:

> [!div class="checklist"]
> * Compreenda o esquema de dados de registo
> * Escreva e execute consultas simples, e modifique o intervalo de tempo para consultas
> * Filtrar, classificar e resultados de consulta de grupo
> * Ver, modificar e partilhar visuais dos resultados da consulta
> * Guardar, carregar, exportar e copiar consultas e resultados

Para obter mais informações sobre consultas de registo, consulte [a visão geral das consultas de registo no Azure Monitor](log-query-overview.md).<br/>
Para obter um tutorial detalhado sobre consultas de registo de escrita, consulte [Começar com consultas de registo no Azure Monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Abrir analítica de registo
Para utilizar o Log Analytics, tem de ser inscrito numa conta Azure. Se não tiver uma conta Azure, [crie uma de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

Para completar a maioria dos passos neste tutorial, você pode usar [este ambiente de demonstração](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), que inclui abundância de dados de amostra. Com o ambiente de demonstração, não será capaz de guardar consultas ou pin resulta num dashboard.

Também pode utilizar o seu próprio ambiente, se estiver a utilizar o Azure Monitor para recolher dados de registo em pelo menos um recurso Azure. Para abrir um espaço de trabalho Log Analytics, no seu Monitor Azure deixou a navegação, selecione **Logs**. 

## <a name="understand-the-schema"></a>Compreender o esquema
 
Um *esquema* é uma coleção de tabelas agrupadas em categorias lógicas. O esquema de Demonstração tem várias categorias de soluções de monitorização. Por exemplo, a categoria **De Gestão de Registos** contém eventos Windows e Syslog, dados de desempenho e batimentos cardíacos do agente.

As tabelas de esquema aparecem no separador **Tabelas** do espaço de trabalho Log Analytics. As tabelas contêm colunas, cada uma com um tipo de dado mostrado pelo ícone ao lado do nome da coluna. Por exemplo, a tabela **Evento** contém colunas de texto como **Computador** e colunas numéricas como **a EventCategory**.

![O Screenshot mostra a página de Logs do portal Azure com uma nova consulta, destacando o painel de tabelas com computador e EventCategoria em destaque.](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Escrever e executar consultas básicas

O Log Analytics abre com uma nova consulta em branco no **editor de Consulta**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Escrever uma consulta

As consultas de registo do Azure Monitor utilizam uma versão da língua de consulta Kusto. As consultas podem começar com um nome de mesa ou um comando [de pesquisa.](/azure/kusto/query/searchoperator) 

A seguinte consulta recupera todos os registos da tabela **Evento:**

```Kusto
Event
```

O caráter do tubo (/) separa os comandos, pelo que a saída do primeiro comando é a entrada do comando seguinte. Pode adicionar qualquer número de comandos a uma única consulta. A seguinte consulta recupera os registos da tabela **Evento** e, em seguida, procura-os para o **erro** do termo em qualquer propriedade:

```Kusto
Event 
| search "error"
```

Uma única rutura de linha facilita a leitura das consultas. Mais de uma rutura de linha divide a consulta em consultas separadas.

Outra forma de escrever a mesma consulta é:

```Kusto
search in (Event) "error"
```

No segundo exemplo, o comando **de pesquisa** procura apenas registos na tabela **Eventos** para o **erro**do termo .

Por predefinição, o Log Analytics limita as consultas a um intervalo de tempo das últimas 24 horas. Para definir um intervalo de tempo diferente, pode adicionar um filtro **timegenerated** explícito à consulta, ou utilizar o controlo **de intervalo de tempo.**

### <a name="use-the-time-range-control"></a>Utilize o controlo do intervalo de tempo
Para utilizar o controlo de **intervalo de tempo,** selecione-o na barra superior e, em seguida, selecione um valor da lista de dropdown ou selecione **Custom** para criar um intervalo de tempo personalizado.

![Apanhador de tempo](media/get-started-portal/time-picker.png)

- Os valores do intervalo de tempo estão na UTC, que pode ser diferente do seu fuso horário local.
- Se a consulta definir explicitamente um filtro para **TimeGenerated,** o controlo do apanhador de tempo mostra **set in consulta**, e é desativado para evitar um conflito.

### <a name="run-a-query"></a>Run a query (Executar uma consulta)
Para executar uma consulta, coloque o cursor algures dentro da consulta e selecione **Executar** na barra superior ou **Shift** + **pressione**Shift Enter . A consulta vai até encontrar uma linha em branco.

## <a name="filter-results"></a>Filtrar os resultados
Log Analytics limita os resultados a um máximo de 10.000 registos. Uma consulta geral como `Event` devolve demasiados resultados para ser útil. Pode filtrar os resultados da consulta, quer restringindo os elementos de tabela na consulta, quer adicionando explicitamente um filtro aos resultados. A filtragem através dos elementos de tabela devolve um novo conjunto de resultados, enquanto um filtro explícito se aplica ao conjunto de resultados existente.

### <a name="filter-by-restricting-table-elements"></a>Filtrar restringindo elementos de mesa
Para filtrar `Event` os resultados da consulta a eventos **de erro,** restringindo elementos de tabela na consulta:

1. Nos resultados da consulta, selecione a seta de dropdown ao lado de qualquer registo que tenha **Erro** na coluna **EventLevelName.** 
   
1. Nos detalhes expandidos, paire sobre e selecione o **...** ao lado **do EventLevelName**, e, em seguida, selecione **Incluir "Erro"**. 
   
   ![Adicionar filtro à consulta](media/get-started-portal/add-filter.png)
   
1. Note que a consulta no **editor de consulta** mudou agora para:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Selecione **Executar** para executar a nova consulta.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrar filtrando explicitamente os resultados
Para filtrar os resultados da `Event` consulta em eventos de **erro** filtrando os resultados da consulta:

1. Nos resultados da consulta, selecione o ícone **filtro** ao lado da posição de coluna **EventLevelName**. 
   
1. No primeiro campo da janela pop-up, selecione **É igual a**, e no campo seguinte, introduzir *erro*. 
   
1. Selecione **Filtro**.
   
   ![O Screenshot mostra uma tabela de resultados com um menu contextual para filtrar os resultados pelo EventLevelName.](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Ordenar, grupo e selecionar colunas
Para classificar os resultados da consulta por uma coluna específica, como **timeGenerated [UTC],** selecione o título da coluna. Selecione o título novamente para alternar entre a ordem ascendente e descendente.

![Coluna de classificação](media/get-started-portal/sort-column.png)

Outra forma de organizar resultados é por grupos. Para agrupar os resultados por uma coluna específica, arraste o cabeçalho da coluna para a barra acima da tabela de resultados rotulada **Arraste um cabeçalho de coluna e deixe-o aqui para grupo por aquela coluna**. Para criar subgrupos, arraste outras colunas para a barra superior. Pode reorganizar a hierarquia e separar os grupos e subgrupos no bar.

![O Screenshot mostra os resultados da consulta com subgrupos para EventLevelName e Computador.](media/get-started-portal/groups.png)

Para ocultar ou mostrar colunas nos resultados, selecione **Colunas** acima da tabela e, em seguida, selecione ou desmarca as colunas desejadas na lista de retiradas.

![Selecionar colunas](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Ver e modificar gráficos
Também pode ver resultados de consulta em formatos visuais. Insira a seguinte consulta como exemplo:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Por predefinição, os resultados aparecem numa tabela. Selecione **Gráfico** acima da tabela para ver os resultados numa vista gráfica.

![Gráfico de barras](media/get-started-portal/bar-chart.png)

Os resultados aparecem num gráfico de barras empilhado. Selecione outras opções como **A Coluna Empilhada** ou **Torta** para mostrar outras visões dos resultados.

![Gráfico circular](media/get-started-portal/pie-chart.png)

Pode alterar propriedades da vista, como eixos x e y, ou preferências de agrupamento e divisão, manualmente a partir da barra de controlo.

Também pode definir a vista preferida na própria consulta, utilizando o acionador [de renderização.](/azure/kusto/query/renderoperator)

## <a name="pin-results-to-a-dashboard"></a>Pin resulta de um dashboard

Para fixar uma tabela de resultados ou gráfico do Log Analytics para um painel Azure partilhado, selecione **Pin para painel** na barra superior. 

![Afixar ao dashboard](media/get-started-portal/pin-dashboard.png)

No **Pino para outra vidraça** do painel, selecione ou crie um painel de instrumentos partilhado para fixar e selecione **Aplicar**. A tabela ou gráfico aparece no painel Azure selecionado.

![Gráfico fixado ao painel](media/get-started-portal/pin-dashboard2.png)

Uma tabela ou gráfico que você pina a um dashboard compartilhado tem as seguintes simplificações: 

- Os dados estão limitados aos últimos 14 dias.
- Uma tabela só mostra até quatro colunas e as sete primeiras filas.
- Gráficos com muitas categorias discretas agrupam automaticamente categorias menos povoadas num **único** caixote.

## <a name="save-load-or-export-queries"></a>Poupe, carregue ou exporte consultas

Uma vez que crie uma consulta, pode guardar ou partilhar a consulta ou os resultados com outros. 

### <a name="save-queries"></a>Guardar consultas

Para salvar uma consulta:

1. **Selecione Guarde** na barra superior.
   
1. No diálogo **Save,** dê à consulta um **Nome,** utilizando os caracteres a-z, A-Z, 0-9, espaço, hífen, sublinhado, período, parênteses ou tubo. 
   
1. Selecione se guardar a consulta como **uma consulta** ou uma **função**. Funções são consultas que outras consultas podem referenciar. 
   
   Para guardar uma consulta em função, forneça um **Alias de Função,** que é um nome curto para outras consultas a utilizar para chamar esta consulta.
   
1. Se estiver num espaço de trabalho log analytics, forneça uma **categoria** para que o **explorador de consultas** utilize para a consulta. (As categorias não estão disponíveis para consultas de Insights de Aplicações)
   
1. Selecione **Guardar**.
   
   ![Guardar função](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Consultas de carga
Para carregar uma consulta guardada, selecione **o explorador de consultas** na parte superior direita. O **painel de exploradores de consulta** abre, listando todas as consultas por categoria. Expanda as categorias ou introduza um nome de consulta na barra de pesquisa e, em seguida, selecione uma consulta para carregá-la no **editor de consulta**. Pode marcar uma consulta como **favorito** selecionando a estrela ao lado do nome de consulta.

![Explorador de consultas](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Consultas de exportação e de partilha
Para exportar uma consulta, selecione **Export** on the top bar e, em seguida, selecione **Export to CSV - todas as colunas**, **Export to CSV - colunas apresentadas**, ou **Export to Power BI (consulta M)** da lista de abandono.

O vídeo que se segue mostra-lhe como integrar o Log Analytics com o Excel.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Para partilhar um link para uma consulta, selecione **Copy link** na barra superior e, em seguida, selecione Copy link **para consulta**, Copy **consulta text**, ou Copy **consulta resultados** para copiar para a área de transferência. Pode enviar o link de consulta a outros que tenham acesso ao mesmo espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo tutorial para saber mais sobre a escrita de consultas de registo do Azure Monitor.
> [!div class="nextstepaction"]
> [Escreva consultas de log monitor Azure](get-started-queries.md)
