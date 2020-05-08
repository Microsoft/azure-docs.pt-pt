---
title: Livros de Trabalho do Monitor Azure- Transforme os dados da JSON com a JSONPath
description: Como utilizar o JSONPath nos livros de trabalho do Monitor Azure para transformar os resultados dos dados da JSON devolvidos por um ponto final consultado ao formato que deseja.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 58a2657f6b9aee101384146c4ebb43023953bfcb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892215"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Como usar o JSONPath para transformar dados da JSON em livros de trabalho

Os livros de trabalho são capazes de consultar dados de muitas fontes. Alguns pontos finais, como [o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) ou o endpoint personalizado, podem devolver resultados em JSON. Se os dados jSON devolvidos pelo ponto final consultado não estiverem configurados num formato que desejar, o JSONPath pode ser usado para transformar os resultados.

JSONPath é uma linguagem de consulta para JSON que é semelhante a XPath para XML. Tal como o XPath, o JSONPath permite a extração e filtração de dados a partir da estrutura JSON.

Ao utilizar a transformação jSONPath, os autores do livro são capazes de converter jSON em uma estrutura de mesa. A tabela pode então ser usada para traçar visualizações de [livros.](workbooks-visualizations.md)

## <a name="using-jsonpath"></a>Usando jsonpath

1. Mude o livro para o modo de edição clicando no item da barra de ferramentas *Editar.*
2. Utilize o link *adicionar* > *consulta* para adicionar um controlo de consulta ao livro de trabalho.
3. Selecione a fonte de dados como *JSON*.
4. Use o editor da JSON para introduzir o seguinte corte JSON
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

Vamos supor que nos é dado o objeto jSON acima como uma representação do inventário de uma loja. A nossa tarefa é criar uma tabela dos livros disponíveis da loja, listando os seus títulos, autores e preços.

1. Selecione o separador *Definições* de Resultados e mude o formato de resultadopara *Caminho JSON*.
2. Aplicar as seguintes Definições do Caminho JSON:

    Mesa de caminho `$.store.books`JSON: . Este campo representa o caminho da raiz da mesa. Neste caso, preocupamo-nos com o inventário do livro da loja. O caminho da mesa filtra o JSON para a informação do livro.

   | IDs de coluna | Caminho JSON da Coluna |
   |:-----------|:-----------------|
   | Título      | `$.title`        |
   | Autor     | `$.author`       |
   | Preço      | `$.price`        |

    Os IDs da coluna serão os cabeçalhos da coluna. Os campos de caminhos da Coluna JSON representam o caminho desde a raiz da tabela até ao valor da coluna.

1. Aplique as definições acima clicando em *Executar Consulta*

![ Editar item de consulta com fonte de dados JSON e formato de resultado son path](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Passos seguintes
- [Visão geral dos livros](workbooks-overview.md)
- [Grupos em Livros de Monitorização Azure](workbooks-groups.md)
