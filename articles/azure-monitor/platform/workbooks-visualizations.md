---
title: Visualizações do livro Azure Monitor
description: Conheça todos os componentes de visualização do livro Azure Monitor, incluindo - texto, gráficos, grades, árvores e gráficos.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658035"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualizações do livro Azure Monitor

Os livros azure Monitor suportam uma série de diferentes estilos de visualizações para acomodar as suas necessidades de reporte. Este artigo fornece exemplos de cada tipo de visualização.

## <a name="text"></a>Texto

Os livros permitem que os autores incluam blocos de texto nos seus livros. O texto pode ser uma análise humana da telemetria, informações para ajudar os utilizadores a interpretar os seus dados, títulos de secção, etc.

![Screenshot da tabela apdex do texto](./media/workbooks-visualizations/apdex.png)

O texto é adicionado através de um controlo de Markdown que fornece um controlo completo da formatação.

![Screenshot de marcação crua que constrói a tabela renderizada](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Adicione um controlo de texto

1. Mude o livro para o modo de edição clicando no item da barra de ferramentas **Editar.**
2. Utilize a ligação **de texto Adicionar** para adicionar um controlo de texto ao livro.
3. Adicione Markdown ao controlo.
4. Clique no botão **de edição feito** para ver o texto formatado.

> [!TIP]
> Use esta [folha de batota Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) para aprender sobre diferentes opções de formatação.

## <a name="charts"></a>Gráficos

Os livros de trabalho permitem que os dados de monitorização sejam apresentados como gráficos. Os tipos de gráficos suportados incluem linha, bar, bar categórico, área, lotes de dispersão, torta e tempo. Os autores podem optar por personalizar a altura, largura, paleta de cores, lenda, títulos, mensagem sem dados, etc. do gráfico.

Livros de trabalho suportam gráficos de suporte tanto para registos como para fontes de dados métricas. 

### <a name="adding-a-log-chart"></a>Adicionar um gráfico de registo

1. Mude o livro para o modo de edição clicando no item da barra de ferramentas **Editar.**
2. Utilize o link **adicionar** consulta para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a atingir.
4. Utilize o editor da Consulta para introduzir o [KQL](https://docs.microsoft.com/azure/kusto/query/) para a sua análise (por exemplo, tendência de pedidos).
5. Delineie a visualização para uma das: **Área,** **Bar,** **Bar (categórica)** , **Linha,** **Tarte, Dispersão,** ou **Tempo**.
6. Definir outros parâmetros se necessário - como intervalo de tempo, visualização, tamanho, paleta de cores e lenda.

![Screenshot do gráfico de registo no modo de edição](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parâmetros de gráfico de log

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a usar | Log, Azure Resource Graph, etc. |
| `Resource Type` | O tipo de recurso para o alvo | Insights de aplicação, Log Analytics ou Azure-first |
| `Resources` | Um conjunto de recursos para obter o valor das métricas de | MyApp1 |
| `Time Range` | A janela do tempo para ver o gráfico de registo | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a utilizar | Área, Bar, Linha, Torta, Dispersão, Tempo, Bar categórico |
| `Size` | O tamanho vertical do controlo | Pequeno, médio, grande ou cheio |
| `Color palette` | A paleta de cores para usar na tabela. Ignorado em modo multimétrico ou segmentado. | Azul, verde, vermelho, etc. |
| `Legend` | A função de agregação para usar para a lenda | Soma ou Média de valores ou Max, Min, Primeiro, Último valor |
| `Query` | Qualquer consulta KQL que retorne os dados no formato esperado pela visualização do gráfico | _solicita \| make-series Pedidos = contagem() padrão = 0 no carimbo de tempo de há pouco(1d) até agora() passo 1h_ |

### <a name="adding-a-metric-chart"></a>Adicionar um gráfico métrico

1. Mude o livro para o modo de edição clicando no item da barra de ferramentas **Editar.**
2. Utilize a ligação **métrica Adicionar** para adicionar um controlo métrico ao livro.
3. Selecione um tipo de recurso (por exemplo, Conta de Armazenamento), os recursos para o alvo, o espaço de nome métrico e o nome, e a agregação a utilizar.
4. Definir outros parâmetros se necessário - como intervalo de tempo, split-by, visualização, tamanho e paleta de cores.

![Screenshot do gráfico métrico no modo de edição](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parâmetros de gráficométrico

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Resource Type` | O tipo de recurso para o alvo | Armazenamento ou Máquina Virtual. |
| `Resources` | Um conjunto de recursos para obter o valor das métricas de | MyStorage1 |
| `Namespace` | O espaço de nome com a métrica | Armazenamento > Blob |
| `Metric` | A métrica para visualizar | Armazenamento > Blob > Transações |
| `Aggregation` | A função de agregação para aplicar à métrica | Soma, Conde, Média, etc. |
| `Time Range` | A janela do tempo para ver a métrica em | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a utilizar | Área, Bar, Linha, Dispersão, Grelha |
| `Split By` | Opcionalmente dividir a métrica em uma dimensão | Transações por tipo Geo |
| `Size` | O tamanho vertical do controlo | Pequeno, médio ou grande |
| `Color palette` | A paleta de cores para usar na tabela. Ignorado se o parâmetro `Split by` for usado | Azul, verde, vermelho, etc. |

## <a name="grids"></a>Grelhas

Grelhas ou tabelas são uma forma comum de apresentar dados aos utilizadores. Os livros de recção permitem que os utilizadores modelem individualmente as colunas da grelha para fornecer uma UI rica para os seus relatórios.

O exemplo abaixo mostra uma grelha que combina ícones, mapas de calor e barras de faísca para apresentar informações complexas. O livro também fornece triagem, uma caixa de pesquisa e um botão de análise.

![Screenshot da grelha baseada em tronco](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Adicionar uma grelha baseada em tronco

1. Mude o livro para o modo de edição clicando no item da barra de ferramentas **Editar.**
2. Utilize o link **adicionar** consulta para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a atingir.
4. Utilize o editor da Consulta para introduzir o KQL para a sua análise (por exemplo, VMs com memória abaixo de um limiar)
5. Definir a visualização para **Grid**
6. Definir outros parâmetros se necessário - como intervalo de tempo, tamanho, paleta de cores e lenda.

![Screenshot da consulta de grelha baseada em tronco](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Azulejos

Os azulejos são uma forma muito útil de apresentar dados sumários nos livros. A imagem abaixo mostra um caso de uso comum de azulejos - resumo do nível da aplicação em cima de uma grelha detalhada.

![Screenshot da vista sumária de azulejos](./media/workbooks-visualizations/tiles-summary.png)

Suporte de azulejos de livro mostrando um título, legenda, texto grande, ícones, gradientes de base métrica, linha de faísca/barras, rodapé, etc.

### <a name="adding-a-tile"></a>Adicionar um azulejo

1. Mude o livro para o modo de edição clicando no item da barra de ferramentas _Editar._
2. Utilize o link **adicionar** consulta para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a atingir.
4. Use o editor de Consulta para introduzir o KQL para a sua análise
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Tamanho definido para **completo**
6. Detete a visualização para **azulejos**
7. Clique no botão Definições de **Azulejos** para abrir o painel de definições
8. Em **Campos de Azulejos,** conjunto:
    * Denominação: `name`
    * Esquerda: `Requests`, Renderizador: `Big Number`, Paleta de Cores: `Green to Red`, Valor do Mina: `0`
    * Fundo: `appName`
9. Clique no botão **Guardar e Fechar** na parte inferior do painel.

![Screenshot da vista sumária de azulejos](./media/workbooks-visualizations/tile-settings.png)

É assim que os azulejos vão ficar no modo de leitura:

![Screenshot da vista sumária de azulejos](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Árvores

Os livros de trabalho apoiam vistas hierárquicas através de grades de árvores. As árvores permitem que algumas linhas sejam expansíveis para o próximo nível para uma experiência de perfuração.

O exemplo abaixo mostra as métricas de saúde dos recipientes (tamanho do conjunto de trabalho) visualizadas como uma grelha de árvores. Os nós de alto nível aqui são os nós do Serviço Azure Kubernetes (AKS), o próximo nível são as cápsulas e o nível final são os contentores. Note que ainda pode formatar as suas colunas como numa grelha (mapa de calor, ícones, link). A fonte de dados subjacente neste caso é um espaço de trabalho log Analytics com registos AKS.

![Screenshot da vista sumária de azulejos](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Adicionar uma grade de árvore
1. Mude o livro para o modo de edição clicando no item da barra de ferramentas _Editar._
2. Utilize o link **adicionar** consulta para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a atingir.
4. Use o editor de Consulta para introduzir o KQL para a sua análise
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
5. Definir a visualização para **Grid**
6. Clique no botão **Definições** da Coluna para abrir o painel de definições
7. Na secção **Árvore/Grupo Por Definições** na parte inferior, coloque:
    * Tipo de árvore: `Parent/Child`
    * Id Field: `Id`
    * Campo de Identificação dos Pais: `ParentId`
    * Mostre o expansor em: `Name`
    * Expandir o nível superior da árvore: `checked`
8. Na secção _Colunas_ na parte superior, coloque:
    * _ID_ - Column Renderer: `Hidden`
    * _Id dos pais_ - Column Renderer: `Hidden`
    * _Pedidos_ - Renderizador de coluna: `Bar`, Cor: `Blue`, Valor Mínimo: `0`
9. Clique no botão _Guardar e Fechar_ na parte inferior do painel.    

![Screenshot da vista sumária de azulejos](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Definições de árvores

| Definição | Explicação |
|:------------- |:-------------|
| `Id Field` | A identificação única de cada linha da grelha |
| `Parent Id Field` | A identificação do pai da linha atual |
| `Show the expander on` | A coluna para mostrar o expansor da árvore. É comum as grelhas de árvores esconderem o seu id e o campo de identificação dos pais porque não são muito legíveis. Em vez disso, o expansor aparece num campo com um valor mais legível - como o nome da entidade |
| `Expand the top level of the tree` | Se verificada, a grelha da árvore será expandida ao nível superior. Útil se quiser mostrar mais informações por padrão |

## <a name="graphs"></a>Gráficos

Os livros de trabalho suportam visualizar gráficos arbitrários com base em dados de registos para mostrar as relações entre entidades de monitorização.

O gráfico abaixo mostra dados que fluem dentro/para fora de um computador através de várias portas de/para computadores externos. É colorida por tipo (computador vs. porta vs. IP externo) e os tamanhos da borda correspondem à quantidade de dados que fluem pelo meio. Os dados subjacentes provêm da consulta Da KQL que visa as ligações VM.

![Screenshot da vista sumária de azulejos](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Adicionar um gráfico
1. Mude o livro para o modo de edição clicando no item da barra de ferramentas _Editar._
2. Utilize o link **adicionar** consulta para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a atingir.
4. Use o editor de Consulta para introduzir o KQL para a sua análise
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
7. Definir a visualização para **Graph**
8. Clique no botão **Definições** de Gráfico para abrir o painel de definições
9. Em _Layout Fields_ na parte inferior, definido:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. Em _Definições de formato_ de nó na parte superior, definido:
    * _Conteúdo superior_- Coluna de utilização: `Name`, Renderizador de coluna: `Text`
    * _Conteúdo do Centro_- Coluna de utilização: `Calls`, Renderizador de coluna: `Big Number`, Paleta de Cores: `None`
    * _Conteúdo inferior_- Coluna de utilização: `Kind`, Renderizador de coluna: `Text`
10. Clique no botão _Guardar e Fechar_ na parte inferior do painel.

![Screenshot da vista sumária de azulejos](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Passos seguintes

* [Implementar](workbooks-automate.md) livros de trabalho com o Gestor de Recursos Azure.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
