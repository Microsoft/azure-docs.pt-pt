---
title: Azure Monitor Workbooks - Transforme os dados da JSON com o JSONPath
description: Como utilizar o JSONPath nos livros do Azure Monitor para transformar os resultados dos dados JSON devolvidos por um ponto final questionado ao formato desejado.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 142853f0d5ed787d0b7aaee1bb118a001f144227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722930"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Como usar o JSONPath para transformar dados da JSON em livros

Os livros de trabalho são capazes de consultar dados de muitas fontes. Alguns pontos finais, como [o Azure Resource Manager](../../azure-resource-manager/management/overview.md) ou o ponto final personalizado, podem devolver resultados em JSON. Se os dados JSON devolvidos pelo ponto final questionado não forem configurados num formato que deseje, o JSONPath pode ser utilizado para transformar os resultados.

JSONPath é uma linguagem de consulta para JSON que é semelhante a XPath para XML. Tal como o XPath, o JSONPath permite a extração e filtração de dados fora da estrutura JSON.

Ao utilizar a transformação JSONPath, os autores do livro são capazes de converter JSON numa estrutura de mesa. A tabela pode então ser usada para traçar [visualizações de livros.](./workbooks-overview.md#visualizations)

## <a name="using-jsonpath"></a>Utilização do JSONPath

1. Mude o livro para editar o modo clicando no item da barra de ferramentas *Editar.*
2. Utilize o link *de* consulta  >  *Add Add* para adicionar um controlo de consulta ao livro de trabalho.
3. Selecione a fonte de dados como *JSON*.
4. Use o editor JSON para inserir o seguinte snippet JSON
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Vamos supor que nos é dado o objeto JSON acima como uma representação do inventário de uma loja. A nossa tarefa é criar uma tabela dos livros disponíveis da loja, listando os seus títulos, autores e preços.

1. Selecione o *separador Definições de Resultados* e altere o formato de resultado para *JSON Path*.
2. Aplicar as seguintes definições do caminho JSON:

    Tabela de Caminhos JSON: `$.store.books` . Este campo representa o caminho da raiz da mesa. Neste caso, preocupamo-nos com o inventário de livros da loja. O caminho da mesa filtra o JSON à informação do livro.

   | IDs de coluna | Rota JSON da Coluna |
   |:-----------|:-----------------|
   | Título      | `$.title`        |
   | Autor     | `$.author`       |
   | Preço      | `$.price`        |

    Os IDs da coluna serão os cabeçalhos das colunas. Os campos de caminhos da Coluna JSON representam o caminho desde a raiz da tabela até ao valor da coluna.

1. Aplicar as definições acima clicando *em Consulta de Execução*

![ Item de consulta de edição com fonte de dados JSON e formato de resultados JSON Path](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Passos seguintes
- [Visão geral dos livros](./workbooks-overview.md)
- [Grupos em livros de monitores Azure](workbooks-groups.md)