---
title: Livro de trabalho do Monitor Azure baixa parâmetros
description: Simplificar relatórios complexos com livros pré-construídos e parâmetros personalizados que contenham parâmetros de dropdown
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658290"
---
# <a name="workbook-drop-down-parameters"></a>Livro de trabalho baixa parâmetros

As desistências permitem ao utilizador recolher um ou mais valores de entrada a partir de um conjunto conhecido (por exemplo, selecione um dos pedidos da sua aplicação). As desistências fornecem uma forma amigável de recolher inputs arbitrários dos utilizadores. As quedas são especialmente úteis para permitir a filtragem nos seus relatórios interativos. 

A maneira mais fácil de especificar uma queda é fornecendo uma lista estática na definição do parâmetro. Uma forma mais interessante é obter a lista dinamicamente através de uma consulta KQL. As definições do parâmetro também permitem especificar se é simples ou multi-selecionado, e se é multi-selecionado, como o conjunto de resultados deve ser formatado (delimitador, citação, etc.).

## <a name="creating-a-static-drop-down-parameter"></a>Criar um parâmetro estático de queda

1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro:`Environment`
    2. Tipo de parâmetro:`Drop down`
    3. Necessário:`checked`
    4. Permitir: `multiple selection``unchecked`
    5. Obtenha dados de:`JSON`
5. No bloco de texto JSON Entrada, insira este corte json:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Acerte `Update` no botão azul.
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.
8. O parâmetro Ambiente será uma queda com os três valores.

    ![Imagem mostrando a criação de uma afogada estática](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Criar uma queda estática com grupos de itens
Se o seu resultado de consulta/json contiver um campo de "grupo", o dropdown mostrará grupos de valores. Siga a amostra acima, mas use o seguinte json em vez disso:
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
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Criar um parâmetro dinâmico de drop-down
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro:`RequestName`
    2. Tipo de parâmetro:`Drop down`
    3. Necessário:`checked`
    4. Permitir: `multiple selection``unchecked`
    5. Obtenha dados de:`Query`
5. No bloco de texto JSON Entrada, insira este corte json:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Acerte `Run Query` no botão azul.
2. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.
3. O parâmetro RequestName será uma gota de todos os nomes de todos os pedidos na aplicação.

    ![Imagem mostrando a criação de uma queda dinâmica](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Referenciando o parâmetro de descida
### <a name="in-kql"></a>Em KQL
1. Adicione um controlo de consulta ao livro e selecione um recurso Application Insights.
2. No editor da KQL, insira este corte

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Isto expande o tempo de avaliação de consulta para:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Corra a consulta para ver os resultados. Opcionalmente, torná-lo como um gráfico.

    ![Imagem mostrando uma queda referenciada em KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Valor, etiqueta, seleção e grupo de parâmetros
A consulta usada no parâmetro dinâmico de drop-down acima apenas devolve uma lista de valores que são prestados fielmente na queda. Mas e se quisesse sê-lo com um nome de exibição diferente, ou um destes para ser selecionado? Os parâmetros de desminusão permitem-no através do valor, etiqueta, seleção e colunas de grupo.

A amostra abaixo mostra como obter uma lista de dependências de Application Insights cujos nomes de exibição são modelados com um emoji, tem o primeiro selecionado, e é agrupado por nomes de operação.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Desater as opções do parâmetro
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | O valor selecionado | GET fabrikamaccount |
| `{DependencyName:label}` | O rótulo selecionado | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | O valor selecionado | GET fabrikamaccount |

## <a name="multiple-selection"></a>Seleção múltipla
Os exemplos até agora configuram explicitamente o parâmetro para selecionar apenas um valor na queda. Os parâmetros de `multiple selection` desbloqueio também suportam - `Allow multiple selection` permitindo que isto seja tão simples como verificar a opção. 

O utilizador também tem a opção de especificar `delimiter` o `quote with` formato do conjunto de resultados através das definições e definições. O padrão apenas devolve os valores como uma coleção nesta forma: 'a', 'b', 'c'. Têm também a opção de limitar o número de seleções.

O KQL referenciando o parâmetro terá de mudar para trabalhar com o formato do resultado. A forma mais comum de `in` o permitir é através do operador.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Aqui está um exemplo para a entrega multi-selecionada no trabalho:

![Imagem mostrando um parâmetro de entrega multi-selecionado](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
