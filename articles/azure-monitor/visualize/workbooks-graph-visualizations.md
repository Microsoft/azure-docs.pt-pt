---
title: Visualizações de gráficos de livro do Azure Monitor
description: Saiba mais sobre todas as visualizações de gráficos do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100620661"
---
# <a name="graph-visualizations"></a>Visualizações de gráficos

Os livros de trabalho suportam a visualização de gráficos arbitrários com base em dados de registos para mostrar as relações entre entidades de monitorização.

O gráfico abaixo mostra dados que fluem dentro/para fora de um computador através de várias portas de/para computadores externos. É colorida por tipo (computador vs. porta vs. IP externo) e os tamanhos das bordas correspondem à quantidade de dados que fluem entre eles. Os dados subjacentes provêm da consulta KQL que visa as ligações VM.

[![Screenshot da vista sumária do azulejo](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Adicionar um gráfico
1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise.

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

5. Definir a visualização para **Gráfico**
6. Selecione o **botão Definições** de Gráfico para abrir o painel de definições
7. Em _Campos de Layout_ na parte inferior, definido:
    * Nó Id: `Id`
    * Id de origem: `SourceId`
    * ID do alvo: `TargetId`
    * Etiqueta de borda: `None`
    * Tamanho da borda: `Calls`
    * Tamanho do nó: `None`
    * Tipo de coloração: `Categorical`
    * Campo de cor de nó: `Kind`
    * Paleta de cores: `Pastel`
8. Nas _Definições do formato do nó_ na parte superior, ajuste:
    * _Conteúdo superior_- Use Coluna: `Name` , Renderer de coluna: `Text`
    * _Conteúdo do centro_- Use Coluna: `Calls` , Renderer coluna: `Big Number` , Paleta de cores: `None`
    * _Conteúdo inferior_- Utilizar coluna: `Kind` , Renderer de colunas: `Text`
9. Selecione o botão _Guardar e Fechar_ na parte inferior do painel.

[![Screenshot da vista sumária do azulejo com a consulta e as definições acima.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Definições de gráfico

| Definição         | Explicação                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Seleciona uma coluna que fornece o ID único de nós no gráfico. O valor da coluna pode ser de corda ou de um número. |
| `Source Id`     | Seleciona uma coluna que fornece os IDs dos nós de origem para arestas no gráfico. Os valores devem mapear para um valor na coluna _Node Id._ |
| `Target Id`     | Seleciona uma coluna que fornece os IDs dos nós-alvo para arestas no gráfico. Os valores devem mapear para um valor na coluna _Node Id._ |
| `Edge Label`    | Seleciona uma coluna que fornece etiquetas de borda no gráfico.                                                            |
| `Edge Size`     | Seleciona uma coluna que fornece a métrica da qual as larguras das bordas serão baseadas.                                |
| `Node Size`     | Seleciona uma coluna que fornece a métrica em que as áreas do nó serão baseadas.                                 |
| `Coloring Type` | Usado para escolher o esquema de coloração do nó.                                                                            |

## <a name="node-coloring-types"></a>Tipos de coloração de nó

| Tipo de coloração | Explicação |
|:------------- |:------------|
| `None`        | Todos os nós têm a mesma cor. |
| `Categorical` | Os nós são atribuídos cores com base no valor ou categoria de uma coluna no conjunto de resultados. No exemplo acima, a coloração baseia-se na coluna _Tipo_ do conjunto de resultados. As paletas suportadas são `Default` `Pastel` , e `Cool tone` .  |
| `Field Based` | Neste tipo, uma coluna fornece valores RGB específicos para usar para o nó. Proporciona a maior flexibilidade, mas geralmente requer mais trabalho para permitir.  |

## <a name="node-format-settings"></a>Definições de formato de nó

Os autores de gráficos podem especificar que conteúdo vai para as diferentes partes de um nó: topo, esquerda, centro, direita e fundo. Os gráficos podem utilizar qualquer um dos suportes de livros de renderização (texto, grande número, linhas de faísca, ícone, etc.).

## <a name="field-based-node-coloring"></a>Coloração do nó baseado no campo

1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise.

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
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Definir a visualização para *Gráfico*
6. Selecione o **botão Graph Settings**  para abrir o painel de definições.
7. Em *Campos de Layout* na parte inferior, definido:
    * ID do nó:`Id`
    * Id de origem: `SourceId`
    * ID do alvo: `TargetId`
    * Etiqueta de borda: `None`
    * Tamanho da borda: `Calls`
    * Tamanho do nó: `Node`
    * Tipo de coloração: `Field Based`
    * Campo de cor de nó: `Color`
8. Nas *Definições do Formato nó no* topo, introduza o seguinte.
    * No *Top Content*, conjunto:
        * Coluna de utilização: `Name` .
        * Renderizador de colunas: `Text` .
    * No *Conteúdo do Centro,* conjunto:
        * Coluna de utilização: `Calls`
        * Renderer coluna: `Big Number`
        * Paleta de cores: `None`
    * No *Conteúdo inferior,* conjunto:
        * Coluna de utilização: `Kind`
        * Renderizador de colunas: `Text` .
9. Selecione o **botão Guardar e Fechar** na parte inferior do painel.

[![Screenshot mostrando a criação de uma visualização de gráfico com coloração do nó de base de campo.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Os gráficos também suportam o renderizador de barras compósitos. Para saber mais, visite a documentação do [Bar Composto.](workbooks-composite-bar.md)
* Saiba mais sobre as [fontes de dados](workbooks-data-sources.md) que pode utilizar nos livros.
