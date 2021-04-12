---
title: Obtenha todos os conjuntos de dados API
description: Utilize esta API para obter todos os conjuntos de dados disponíveis para análise de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583943"
---
# <a name="get-all-datasets-api"></a>Obtenha todos os conjuntos de dados API

A API obtém todos os conjuntos de dados que a API obtém todos os conjuntos de dados disponíveis. Os conjuntos de dados listam as tabelas, colunas, métricas e intervalos de tempo.

**Solicitar sintaxe**

| **Método** | **URI do pedido** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Cabeçalho de pedido**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatório. O Azure Ative Directory (Azure AD) acesso no formulário `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Parâmetro do caminho**

Nenhum

**Parâmetro de consulta**

| **Nome do parâmetro** | **Tipo** | **Obrigatório** | **Descrição** |
| --- | --- | --- | --- |
| `datasetName` | cadeia (de carateres) | No | Filtrar para obter detalhes de apenas um conjunto de dados |

**Solicitar carga útil**

Nenhum

**Glossário**

Nenhum

**Response**

A carga útil de resposta é estruturada da seguinte forma:

Código de resposta: 200, 400, 401, 403, 404,500

Exemplo de carga útil de resposta:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Glossário**

Esta tabela define os elementos-chave na resposta:

| **Parâmetro** | **Descrição** |
| --- | --- |
| `DatasetName` | Nome do conjunto de dados que este objeto de matriz define |
| `SelectableColumns` | Colunas em bruto que podem ser especificadas nas colunas selecionadas |
| `AvailableMetrics` | Nomes de colunas agregação/métrica que podem ser especificados nas colunas selecionadas |
| `AvailableDateRanges` | Intervalo de datas que pode ser usado em consultas de relatório para o conjunto de dados |
| `NextLink` | Ligue para a página seguinte se os dados forem paginados |
| `TotalCount` | Número de conjuntos de dados na matriz de valor |
| `StatusCode` | Código de resultados. Os valores possíveis são 200, 400, 401, 403, 500 |
