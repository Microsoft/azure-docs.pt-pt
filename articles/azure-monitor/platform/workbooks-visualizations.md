---
title: Visualizações de livros do Azure Monitor
description: Saiba mais sobre todos os componentes de visualização do livro do Azure Monitor, incluindo - texto, gráficos, grades, árvores e gráficos.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 443751ee974dae3c426f36cbc262bc1a3af65bd0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504942"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualizações de livros do Azure Monitor

Os livros do Azure Monitor suportam uma série de diferentes estilos de visualizações para acomodar as suas necessidades de reporte. Este artigo fornece exemplos de cada tipo de visualização.

## <a name="text"></a>Texto

Os livros permitem que os autores incluam blocos de texto nos seus livros. O texto pode ser a análise humana da telemetria, informação para ajudar os utilizadores a interpretar os seus dados, títulos de secção, etc.

![Screenshot da tabela de texto apdex](./media/workbooks-visualizations/apdex.png)

O texto é adicionado através de um controlo Markdown que prevê um controlo de formatação completo.

![Screenshot de marcação bruta que constrói a tabela renderizada](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Adicionar um controlo de texto

1. Mude o livro para editar o modo clicando no item da barra de ferramentas **Editar.**
2. Utilize o link **de texto Adicionar** para adicionar um controlo de texto ao livro.
3. Adicione o Markdown ao controlo.
4. Clique no botão **de edição feito** para ver o texto formatado.

> [!TIP]
> Utilize esta [folha de batota Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) para aprender sobre diferentes opções de formatação.

## <a name="charts"></a>Gráficos

Os livros permitem que os dados de monitorização sejam apresentados como gráficos. Os tipos de gráficos suportados incluem linha, bar, bar categórico, área, parcelas de dispersão, torta e tempo. Os autores podem optar por personalizar a altura, largura, paleta de cores, lenda, títulos, mensagem sem dados, etc. do gráfico.

Os livros de trabalho suportam gráficos tanto para registos como para fontes de dados métricas. 

### <a name="adding-a-log-chart"></a>Adicionar um gráfico de registo

1. Mude o livro para editar o modo clicando no item da barra de ferramentas **Editar.**
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o [KQL](/azure/kusto/query/) para a sua análise (por exemplo, tendência de pedidos).
5. Coloque a visualização numa das: **Área,** **Bar,** **Bar (categórica)**, **Linha,** **Tarte,** **Dispersão,** ou **Hora**.
6. Desa ajuste outros parâmetros se necessário - como intervalo de tempo, visualização, tamanho, paleta de cores e lenda.

![Screenshot do gráfico de registo no modo de edição](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parâmetros do gráfico de registo

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a usar | Log, Azure Resource Graph, etc. |
| `Resource Type` | O tipo de recurso para o alvo | Insights de Aplicação, Log Analytics ou Azure-first |
| `Resources` | Um conjunto de recursos para obter o valor das métricas de | MyApp1 |
| `Time Range` | A janela de tempo para ver o gráfico de registo | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a usar | Área, Bar, Linha, Torta, Dispersão, Tempo, Bar Categórico |
| `Size` | O tamanho vertical do controlo | Pequeno, médio, grande ou cheio |
| `Color palette` | A paleta de cores para usar na tabela. Ignorado em modo multi-métrico ou segmentado. | Azul, verde, vermelho, etc. |
| `Legend` | A função de agregação para usar para a lenda | Soma ou Média de valores ou Max, Min, Primeiro, Último valor |
| `Query` | Qualquer consulta KQL que retorne dados no formato esperado pela visualização do gráfico | _solicita \| pedidos de séries de pedidos = contagem() predefinição = 0 em tempotamp de ago(1d) to now() passo 1h_ |

### <a name="adding-a-metric-chart"></a>Adicionar um gráfico métrico

1. Mude o livro para editar o modo clicando no item da barra de ferramentas **Editar.**
2. Utilize a ligação **métrica Adicionar** para adicionar um controlo métrico ao livro.
3. Selecione um tipo de recurso (por exemplo, Conta de Armazenamento), os recursos para o destino, o espaço de nome métrico e o nome, e a agregação a utilizar.
4. Desa ajuste outros parâmetros se necessário - como intervalo de tempo, split-by, visualização, tamanho e paleta de cores.

![Screenshot do gráfico métrico no modo de edição](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parâmetros de gráfico métrico

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Resource Type` | O tipo de recurso para o alvo | Armazenamento ou Máquina Virtual. |
| `Resources` | Um conjunto de recursos para obter o valor das métricas de | MyStorage1 |
| `Namespace` | O espaço de nome com a métrica | Bloco de > de armazenamento |
| `Metric` | A métrica para visualizar | Operações > blob > de armazenamento |
| `Aggregation` | A função de agregação para aplicar à métrica | Soma, Contagem, Média, etc. |
| `Time Range` | A janela de tempo para ver a métrica em | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a usar | Área, Bar, Linha, Dispersão, Grelha |
| `Split By` | Opcionalmente dividir a métrica numa dimensão | Transações por Geo tipo |
| `Size` | O tamanho vertical do controlo | Pequeno, médio ou grande |
| `Color palette` | A paleta de cores para usar na tabela. Ignorado se o `Split by` parâmetro for usado | Azul, verde, vermelho, etc. |

## <a name="grids"></a>Grelhas

As redes ou tabelas são uma forma comum de apresentar dados aos utilizadores. Os livros de trabalho permitem que os utilizadores modelem individualmente as colunas da grelha para fornecer uma UI rica para os seus relatórios.

O exemplo abaixo mostra uma grelha que combina ícones, máscaras de calor e barras de faísca para apresentar informações complexas. O livro também fornece triagem, uma caixa de pesquisa e um botão de ir-to-analytics.

![Screenshot da grelha baseada em log](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Adicionar uma grelha baseada em log

1. Mude o livro para editar o modo clicando no item da barra de ferramentas **Editar.**
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise (por exemplo, VMs com memória abaixo de um limiar)
5. Definir a visualização para **Grid**
6. Desa ajuste outros parâmetros se necessário - como intervalo de tempo, tamanho, paleta de cores e lenda.

![Screenshot da consulta de grelha baseada em log](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Mosaicos

Os azulejos são uma forma muito útil de apresentar dados sumários em livros. A imagem abaixo mostra um caso de uso comum de azulejos - resumo de nível de aplicação em cima de uma grelha detalhada.

![Screenshot da vista sumária do azulejo](./media/workbooks-visualizations/tiles-summary.png)

Suporte de azulejos de livros de trabalho mostrando um título, legenda, texto grande, ícones, gradientes de base métrica, linha de faísca/barras, rodapé, etc.

### <a name="adding-a-tile"></a>Adicionando um azulejo

1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Tamanho do conjunto para **o cheio**
6. Definir a visualização para **Azulejos**
7. Clique no botão **Definições de Azulejos** para abrir o painel de definições
8. Em **Tile Fields,** conjunto:
    * Denominação:`name`
    * Esquerda: `Requests` , Renderer: `Big Number` , Paleta de Cores: , Valor `Green to Red` Min:`0`
    * Em baixo:`appName`
9. Clique no botão **Guardar e Fechar** na parte inferior do painel.

![Screenshot da vista sumária do azulejo](./media/workbooks-visualizations/tile-settings.png)

É assim que os azulejos vão ficar no modo de leitura:

![Screenshot da vista sumária do azulejo](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Árvores

Os livros de trabalho suportam vistas hierárquicas através de redes de árvores. As árvores permitem que algumas linhas sejam expansíveis para o próximo nível para uma experiência de perfuração.

O exemplo abaixo mostra métricas de saúde do recipiente (tamanho do conjunto de trabalho) visualizadas como uma grelha de árvore. Os nós de topo aqui são os nós do Serviço Azure Kubernetes (AKS), o próximo nível são cápsulas e o nível final são contentores. Note que ainda pode formatar as suas colunas como numa grelha (mapa térmico, ícones, link). A fonte de dados subjacente neste caso é um espaço de trabalho Log Analytics com registos AKS.

![Screenshot da vista sumária do azulejo](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Adicionando uma grade de árvore
1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise
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
6. Clique no botão **Definições** de Coluna para abrir o painel de definições
7. Na secção **Árvore/Grupo Por Definições** na parte inferior, ajuste:
    * Tipo de árvore:`Parent/Child`
    * Campo de Id:`Id`
    * Campo de Id dos pais:`ParentId`
    * Mostre o expansor em:`Name`
    * Expandir o nível superior da árvore:`checked`
8. Na secção _Colunas_ no topo, set:
    * _ID_ - Renderer de Coluna:`Hidden`
    * _Id dos pais_ - Renderer de colunas:`Hidden`
    * _Pedidos_ - Renderizador de Colunas: `Bar` Cor: `Blue` , Valor Mínimo:`0`
9. Clique no botão _Guardar e Fechar_ na parte inferior do painel.    

![Screenshot da vista sumária do azulejo](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Configurações de árvores

| Definição | Explicação |
|:------------- |:-------------|
| `Id Field` | O ID único de cada linha na grelha |
| `Parent Id Field` | O id do pai da fileira atual |
| `Show the expander on` | A coluna sobre a qual mostrar o expansor da árvore. É comum que as grades de árvores escondam o seu id e o seu campo de identificação dos pais porque não são muito legíveis. Em vez disso, o expansor aparece num campo com um valor mais legível - como o nome da entidade |
| `Expand the top level of the tree` | Se verificada, a grelha da árvore será expandida ao nível superior. Útil se quiser mostrar mais informações por padrão |

## <a name="graphs"></a>Gráficos

Os livros de trabalho suportam a visualização de gráficos arbitrários com base em dados de registos para mostrar as relações entre entidades de monitorização.

O gráfico abaixo mostra dados que fluem dentro/para fora de um computador através de várias portas de/para computadores externos. É colorida por tipo (computador vs. porta vs. IP externo) e os tamanhos das bordas correspondem à quantidade de dados que fluem entre eles. Os dados subjacentes provêm da consulta KQL que visa as ligações VM.

![Screenshot da vista sumária do azulejo](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Adicionar um Gráfico
1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise
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
7. Definir a visualização para **Gráfico**
8. Clique no **botão Definições** de Gráfico para abrir o painel de definições
9. Em _Campos de Layout_ na parte inferior, definido:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. Nas _Definições do formato do nó_ na parte superior, ajuste:
    * _Conteúdo superior_- Use Coluna: `Name` , Renderer de coluna:`Text`
    * _Conteúdo do centro_- Use Coluna: `Calls` , Renderer coluna: `Big Number` , Paleta de cores:`None`
    * _Conteúdo inferior_- Utilizar coluna: `Kind` , Renderer de colunas:`Text`
10. Clique no botão _Guardar e Fechar_ na parte inferior do painel.

![Screenshot da vista sumária do azulejo](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Próximos passos

* [Implementar](workbooks-automate.md) livros de trabalho com o Gestor de Recursos Azure.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
