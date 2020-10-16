---
title: Livro de trabalho do Monitor de Azure desce parâmetros
description: Simplificar relatórios complexos com livros de trabalho parametrizados pré-construídos e personalizados que contenham parâmetros de dropdown
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: ad1e09a0f9d1e0f9e4a748220edf5eced96123f8
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932571"
---
# <a name="workbook-drop-down-parameters"></a>Livro de trabalho deixa cair parâmetros

As descidas permitem ao utilizador recolher um ou mais valores de entrada de um conjunto conhecido (por exemplo, selecione um dos pedidos da sua aplicação). As parades de entrega fornecem uma forma fácil de recolher dos utilizadores inputs arbitrários. As reduções são especialmente úteis para permitir a filtragem nos seus relatórios interativos. 

A maneira mais fácil de especificar uma queda é fornecendo uma lista estática na definição do parâmetro. Uma forma mais interessante é obter a lista dinamicamente através de uma consulta KQL. As definições de parâmetros também permitem especificar se é simples ou multi-selecção, e se é multi-selecção, como o conjunto de resultados deve ser formatado (delimiter, citação, etc.).

## <a name="creating-a-static-drop-down-parameter"></a>Criando um parâmetro estático de queda

1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `Environment`
    2. Tipo de parâmetro: `Drop down`
    3. Necessário: `checked`
    4. `multiple selection`Permitir:`unchecked`
    5. Obtenha dados de: `JSON`
5. No bloco de texto JSON Input, insira este corte json:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. A carregar no `Update` botão azul.
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.
8. O parâmetro Ambiente será uma queda com os três valores.

    ![Imagem mostrando a criação de um afogamento estático](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Criar uma queda estática com grupos de itens

Se o resultado/json da consulta contiver um campo de "grupo", o dropdown apresentará grupos de valores. Siga a amostra acima, mas use o seguinte json:

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![Imagem mostrando um exemplo de um dropdown agrupado](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Criar um parâmetro dinâmico de abandono
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `RequestName`
    2. Tipo de parâmetro: `Drop down`
    3. Necessário: `checked`
    4. `multiple selection`Permitir:`unchecked`
    5. Obtenha dados de: `Query`
5. No bloco de texto JSON Input, insira este corte json:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. A carregar no `Run Query` botão azul.
2. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.
3. O parâmetro RequestName será um drop-down os nomes de todos os pedidos na aplicação.

    ![Imagem mostrando a criação de uma queda dinâmica](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Parâmetro de queda de referência

### <a name="in-kql"></a>Em KQL
1. Adicione um controlo de consulta ao livro de trabalho e selecione um recurso Application Insights.
2. No editor da KQL, insira este corte

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Isto expande-se no tempo de avaliação de consultas para:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Consulte a consulta para ver os resultados. Opcionalmente, torná-lo como um gráfico.

    ![Imagem mostrando uma referência de queda no KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Valor do parâmetro, rótulo, seleção e grupo
A consulta utilizada no parâmetro dinâmico de queda acima apenas devolve uma lista de valores que são prestados fielmente no drop-down. Mas e se quisesses um nome de exibição diferente, ou um destes a ser selecionado? Os parâmetros de queda permitem isso através do valor, etiqueta, seleção e colunas de grupo.

A amostra abaixo mostra como obter uma lista de dependências de Application Insights cujos nomes de exibição são modelados com um emoji, tem o primeiro selecionado, e é agrupado por nomes de operação.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Imagem mostrando um parâmetro drop-down usando opções de valor, etiqueta, seleção e grupo](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Largar opções de parâmetros
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | O valor selecionado | Get fabrikamaccount |
| `{DependencyName:label}` | A etiqueta selecionada | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | O valor selecionado | Get fabrikamaccount |

## <a name="multiple-selection"></a>Seleção múltipla
Os exemplos até agora definem explicitamente o parâmetro para selecionar apenas um valor na queda. Os parâmetros de queda também `multiple selection` suportam - permitindo que isto seja tão simples como verificar a `Allow multiple selection` opção. 

O utilizador também tem a opção de especificar o formato do conjunto de resultados através das `delimiter` definições e `quote with` definições. O padrão apenas devolve os valores como uma coleção desta forma: 'a', 'b', 'c'. Têm também a opção de limitar o número de seleções.

O KQL que refere o parâmetro terá de ser alterado para funcionar com o formato do resultado. A forma mais comum de o permitir é através do `in` operador.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Aqui está um exemplo para o drop-down multi-select no trabalho:

![Imagem mostrando um parâmetro de queda multi-selecionado](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
