---
title: Azure Monitor visualizações da pasta de trabalho
description: Saiba mais sobre todos os componentes de visualizações de pasta de trabalho do Azure Monitor, incluindo-texto, gráficos, grades, árvores e grafos.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3f8f377ac8e65d375f8f78c803db5427fd31007
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394475"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor visualizações da pasta de trabalho

Azure Monitor pastas de trabalho dão suporte a vários estilos diferentes de visualizações para acomodar suas necessidades de relatório. Este artigo fornece exemplos de cada tipo de visualização.

## <a name="text"></a>Texto

As pastas de trabalho permitem que os autores incluam blocos de texto em suas pastas de trabalho. O texto pode ser uma análise humana da telemetria, informações para ajudar os usuários a interpretar seus dados, títulos de seção, etc.

![Captura de tela da tabela Apdex de texto](./media/workbooks-visualizations/apdex.png)

O texto é adicionado por meio de um controle de redução que fornece controle de formatação completo.

![Captura de tela de redução bruta que cria a tabela renderizada](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Adicionar um controle de texto

1. Alterne a pasta de trabalho para o modo de edição clicando no item **Editar** barra de ferramentas.
2. Use o link **Adicionar texto** para adicionar um controle de texto à pasta de trabalho.
3. Adicione redução ao controle.
4. Clique no botão **edição concluído** para ver o texto formatado.

> [!TIP]
> Use esta [página](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) de dicas de redução para saber mais sobre as diferentes opções de formatação.

## <a name="charts"></a>Gráficos

As pastas de trabalho permitem que os dados de monitoramento sejam apresentados como gráficos. Os tipos de gráfico com suporte incluem linha, barra, categórica, área, gráficos de dispersão, pizza e hora. Os autores podem optar por personalizar a altura, a largura, a paleta de cores, a legenda, os títulos, as mensagens sem dados, etc. do gráfico.

As pastas de trabalho dão suporte a gráficos para logs e fontes de dados de métrica. 

### <a name="adding-a-log-chart"></a>Adicionando um gráfico de log

1. Alterne a pasta de trabalho para o modo de edição clicando no item **Editar** barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o [KQL](https://docs.microsoft.com/azure/kusto/query/) para sua análise (por exemplo, tendência de solicitações).
5. Defina a visualização como uma das: **área**, **barra**, **barra (categórico)** , **linha**, **pizza**, **dispersão**ou **hora**.
6. Defina outros parâmetros, se necessário, como intervalo de tempo, visualização, tamanho, paleta de cores e legenda.

![Captura de tela do gráfico de log no modo de edição](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parâmetros do gráfico de log

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a ser usado | Log, grafo de recursos do Azure, etc. |
| `Resource Type` | O tipo de recurso para o destino | Application Insights, Log Analytics ou Azure-First |
| `Resources` | Um conjunto de recursos dos quais obter o valor de métricas | MyApp1 |
| `Time Range` | A janela de tempo para exibir o gráfico de log | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a ser usada | Área, barra, linha, pizza, dispersão, hora, categórica da barra |
| `Size` | O tamanho vertical do controle | Pequeno, médio, grande ou completo |
| `Color palette` | A paleta de cores a ser usada no gráfico. Ignorado no modo de várias métricas ou segmentado. | Azul, verde, vermelho etc. |
| `Legend` | A função de agregação a ser usada para a legenda | Soma ou média de valores ou máximo, mínimo, primeiro, último valor |
| `Query` | Qualquer consulta KQL que retorna dados no formato esperado pela visualização de gráfico | _solicitações \| solicitações da série Make = Count () padrão = 0 no timestamp de atrás (1D) até Now () etapa 1h_ |

### <a name="adding-a-metric-chart"></a>Adicionando um gráfico de métrica

1. Alterne a pasta de trabalho para o modo de edição clicando no item **Editar** barra de ferramentas.
2. Use o link **Adicionar métrica** para adicionar um controle de métrica à pasta de trabalho.
3. Selecione um tipo de recurso (por exemplo, conta de armazenamento), os recursos a serem direcionados, o namespace e o nome da métrica e a agregação a ser usada.
4. Defina outros parâmetros, se necessário, como intervalo de tempo, divisão por, visualização, tamanho e paleta de cores.

![Captura de tela do gráfico de métrica no modo de edição](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parâmetros do gráfico de métrica

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Resource Type` | O tipo de recurso para o destino | Armazenamento ou máquina virtual. |
| `Resources` | Um conjunto de recursos dos quais obter o valor de métricas | MyStorage1 |
| `Namespace` | O namespace com a métrica | Blob de > de armazenamento |
| `Metric` | A métrica a ser visualizada | Armazenamento > blob > transações |
| `Aggregation` | A função de agregação a ser aplicada à métrica | Soma, contagem, média, etc. |
| `Time Range` | A janela de tempo para exibir a métrica | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a ser usada | Área, barra, linha, dispersão, grade |
| `Split By` | Opcionalmente, dividir a métrica em uma dimensão | Transações por tipo geográfico |
| `Size` | O tamanho vertical do controle | Pequeno, médio ou grande |
| `Color palette` | A paleta de cores a ser usada no gráfico. Ignorado se o parâmetro `Split by` for usado | Azul, verde, vermelho etc. |

## <a name="grids"></a>Grelhas

Grades ou tabelas são uma maneira comum de apresentar dados aos usuários. As pastas de trabalho permitem que os usuários estilizam individualmente as colunas da grade para fornecer uma interface do usuário rica para seus relatórios.

O exemplo a seguir mostra uma grade que combina ícones, calor e barras de Spark para apresentar informações complexas. A pasta de trabalho também fornece classificação, uma caixa de pesquisa e um botão ir para a análise.

![Captura de tela da grade baseada em log](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Adicionando uma grade baseada em log

1. Alterne a pasta de trabalho para o modo de edição clicando no item **Editar** barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para sua análise (por exemplo, VMs com memória abaixo de um limite)
5. Definir a visualização como **grade**
6. Defina outros parâmetros, se necessário, como o intervalo de tempo, o tamanho, a paleta de cores e a legenda.

![Captura de tela da consulta de grade com base em log](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Mosaicos

Os blocos são uma maneira muito útil de apresentar dados resumidos em pastas de trabalho. A imagem abaixo mostra um caso de uso comum de blocos-Resumo de nível de aplicativo na parte superior de uma grade detalhada.

![Captura de tela da exibição de resumo do bloco](./media/workbooks-visualizations/tiles-summary.png)

Suporte a blocos da pasta de trabalho mostrando um título, subtítulo, texto grande, ícones, gradientes baseados em métrica, linha/barras do Spark, rodapé, etc.

### <a name="adding-a-tile"></a>Adicionando um bloco

1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho. 
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Definir tamanho como **completo**
6. Definir a visualização como **blocos**
7. Clique no botão **configurações de bloco** para abrir o painel configurações
8. Em **campos de bloco**, defina:
    * Título: `name`
    * Esquerda: `Requests`, processador: `Big Number`, paleta de cores: `Green to Red`, valor mínimo: `0`
    * Inferior: `appName`
9. Clique no botão **salvar e fechar** na parte inferior do painel.

![Captura de tela da exibição de resumo do bloco](./media/workbooks-visualizations/tile-settings.png)

É assim que os blocos serão examinados no modo de leitura:

![Captura de tela da exibição de resumo do bloco](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Tree

As pastas de trabalho dão suporte a exibições hierárquicas por meio de grades de árvore. As árvores permitem que algumas linhas sejam expansíveis no próximo nível para uma experiência de busca detalhada.

O exemplo a seguir mostra as métricas de integridade do contêiner (tamanho do conjunto de trabalho) visualizado como uma grade de árvore. Os nós de nível superior aqui são os nós do AKS (serviço kubernetes do Azure), o próximo nível são pods e o nível final são contêineres. Observe que você ainda pode formatar suas colunas como em uma grade (calor, ícones, link). Nesse caso, a fonte de dados subjacente é um espaço de trabalho Log Analytics com logs AKS.

![Captura de tela da exibição de resumo do bloco](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Adicionando uma grade de árvore
1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho. 
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Definir a visualização como **grade**
6. Clique no botão **configurações de coluna** para abrir o painel configurações
7. Na seção **árvore/grupo por configurações** na parte inferior, defina:
    * Tipo de árvore: `Parent/Child`
    * Campo de ID: `Id`
    * Campo de ID pai: `ParentId`
    * Mostrar o expansor em: `Name`
    * Expanda o nível superior da árvore: `checked`
8. Na seção _colunas_ na parte superior, defina:
    * Renderizador de coluna de _ID_ : `Hidden`
    * _ID pai_ -renderizador de coluna: `Hidden`
    * _Solicitações_ -renderizador de coluna: `Bar`, cor: `Blue`, valor mínimo: `0`
9. Clique no botão _salvar e fechar_ na parte inferior do painel.    

![Captura de tela da exibição de resumo do bloco](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Configurações de árvore

| Definição | Explicação |
|:------------- |:-------------|
| `Id Field` | A ID exclusiva de cada linha na grade |
| `Parent Id Field` | A ID do pai da linha atual |
| `Show the expander on` | A coluna na qual mostrar o expansor de árvore. É comum que as grades de árvore ocultem seus campos ID e ID pai porque não são muito legíveis. Em vez disso, o expansor aparece em um campo com um valor mais legível, como o nome da entidade |
| `Expand the top level of the tree` | Se marcada, a grade de árvore será expandida no nível superior. Útil se você quiser mostrar mais informações por padrão |

## <a name="graphs"></a>Gráficos

As pastas de trabalho dão suporte à visualização de grafos arbitrários com base em dados de logs para mostrar as relações entre entidades de monitoramento.

O gráfico abaixo mostra dados que fluem para dentro/para fora de um computador por meio de várias portas de/para computadores externos. Ele é colorido por tipo (computador versus porta x IP externo) e os tamanhos de borda correspondem à quantidade de dados que fluem entre eles. Os dados subjacentes vêm da consulta KQL direcionamento a conexões de VM.

![Captura de tela da exibição de resumo do bloco](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Adicionando um gráfico
1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho. 
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Definir a visualização como **grafo**
8. Clique no botão **configurações de gráfico** para abrir o painel configurações
9. Em _campos de layout_ na parte inferior, defina:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. Em _configurações de formato de nó_ na parte superior, defina:
    * Coluna de uso de _conteúdo superior_: `Name`, renderizador de coluna: `Text`
    * _Conteúdo do centro_-use a coluna: `Calls`, renderizador de coluna: `Big Number`, paleta de cores: `None`
    * _Conteúdo inferior_-use a coluna: `Kind`, renderizador de coluna: `Text`
10. Clique no botão _salvar e fechar_ na parte inferior do painel.

![Captura de tela da exibição de resumo do bloco](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Passos seguintes

* [Implante](workbooks-automate.md) pastas de trabalho com Azure Resource Manager.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.
