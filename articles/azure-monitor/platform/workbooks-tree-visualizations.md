---
title: Visualizações de árvores de livro do Azure Monitor
description: Conheça todas as visualizações de árvores do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664764"
---
# <a name="tree-visualizations"></a>Visualizações de árvores

Os livros de trabalho suportam vistas hierárquicas através de redes de árvores. As árvores permitem que algumas linhas sejam expansíveis para o próximo nível para uma experiência de perfuração.

O exemplo abaixo mostra métricas de saúde do recipiente (tamanho do conjunto de trabalho) visualizadas como uma grelha de árvore. Os nós de topo aqui são os nós do Serviço Azure Kubernetes (AKS), o próximo nível são cápsulas, e o nível final são contentores. Note que ainda pode formatar as suas colunas como numa grelha (mapa térmico, ícones, link). A fonte de dados subjacente neste caso é um espaço de trabalho Log Analytics com registos AKS.

[![Screenshot da vista sumária do azulejo](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Adicionando uma grade de árvore
1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. **Selecione Adicione** então *Adicione consulta* para adicionar um controlo de consulta de registo ao livro de trabalho.
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
6. Selecione o botão **Definições de Coluna** para abrir o painel de definições
7. Na secção **Árvore/Grupo Por Definições** na parte inferior, ajuste:
    * Tipo de árvore: `Parent/Child`
    * Campo de Id: `Id`
    * Campo de Id dos pais: `ParentId`
    * Mostre o expansor em: `Name`
    * Selecione *Expandir o nível superior da* caixa de verificação da árvore.
8. Na secção _Colunas_ no topo, set:
    * _ID_ - Renderer de Coluna: `Hidden`
    * _Id dos pais_ - Renderer de colunas: `Hidden`
    * _Pedidos_ - Renderizador de Colunas: `Bar` Cor: `Blue` , Valor Mínimo: `0`
9. Selecione o botão **Guardar e Fechar** na parte inferior do painel.

[![Screenshot da vista sumária do azulejo com a consulta e as definições acima.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Configurações de árvores

| Definição | Explicação |
|:------------- |:-------------|
| `Id Field` | A identificação única de todas as linhas da grelha. |
| `Parent Id Field` | A identificação do pai da atual fila. |
| `Show the expander on` | A coluna sobre a qual mostrar o expansor da árvore. É comum que as redes de árvores escondam o seu iD e o campo de identificação dos pais porque não são muito legíveis. Em vez disso, o expansor aparece num campo com um valor mais legível como o nome da entidade. |
| `Expand the top level of the tree` | Se verificada, a grelha da árvore será expandida ao nível superior. Útil se quiser mostrar mais informações por defeito. |

## <a name="grouping-in-a-grid"></a>Agrupamento em uma grelha

O agrupamento permite-lhe construir vistas hierárquicas semelhantes às acima com consultas significativamente mais simples. Perde-se a agregação nos nós internos da árvore, mas isso será aceitável para alguns cenários. Utilize *o Grupo By* para construir vistas de árvores quando o conjunto de resultados subjacente não pode ser transformado numa forma livre adequada, por exemplo: dados de alerta, saúde e métricas.

## <a name="adding-a-tree-using-grouping"></a>Adicionar uma árvore usando o agrupamento

1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. **Selecione Adicione** então *Adicione consulta* para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Desa um pouco de visualização para *grelha.*
2. Selecione o **botão Definições de Coluna** para abrir o painel de definições.
3. Na secção *Árvore/Grupo Por Definições* na parte inferior, ajuste:
    * Tipo de árvore: `Group By`
    * Grupo Por: `App`
    * Então por: `None`
    * Selecione *Expandir o nível superior da* caixa de verificação da árvore.
4. Na secção *Colunas* no topo, set:
    * *App* - Renderer de colunas: `Hidden`
    * *Pedidos* - Renderizador de Colunas: `Bar` Cor: `Blue` , Valor Mínimo: `0`
5. Selecione o botão **Guardar e Fechar** na parte inferior do painel.

[![Screenshot mostrando a criação de uma visualização de árvores em livros](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar um [gráfico em livros de trabalho.](workbooks-graph-visualizations.md)
* Saiba como criar um azulejo em livros de [trabalho.](workbooks-tile-visualizations.md)
