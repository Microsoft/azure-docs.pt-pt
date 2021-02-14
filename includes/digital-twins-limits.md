---
author: baanders
description: incluir arquivo para limites Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 4a69b2ff15fc4857e9fb292d2f753aa68ed875d4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370116"
---
### <a name="functional-limits"></a>Limites funcionais

A tabela que se segue lista os limites funcionais da Azure Digital Twins. 

> [!TIP]
> Para modelar recomendações para operar dentro destes limites funcionais, consulte [as melhores práticas para a conceção de modelos.](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models)

| Área | Funcionalidade | Limite predefinido | Ajustável? |
| --- | --- | --- | --- |
| Recurso do Azure | Número de casos de Gémeos Digitais Azure numa região, por subscrição | 10 | Yes |
| Digital Twins | Número de gémeos num caso de Gémeos Digitais Azure | 200,000 | Yes |
| Digital Twins | Número de relações recebidas com um único gémeo | 5000 | No |
| Digital Twins | Número de relações de saída de um único gémeo | 5000 | No |
| Digital Twins | Tamanho máximo (de corpo JSON num pedido PUT ou PATCH) de um único gémeo | 32 KB | No |
| Digital Twins | Tamanho máximo da carga útil do pedido | 32 KB | No | 
| Encaminhamento | Número de pontos finais para uma única instância Azure Digital Twins | 6 | No |
| Encaminhamento | Número de rotas para uma única instância Azure Digital Twins | 6 | Yes |
| Modelos | Número de modelos dentro de uma única instância Azure Digital Twins | 10,000 | Yes |
| Modelos | Número de modelos que podem ser carregados numa única chamada API | 250 | No |
| Modelos | Tamanho máximo (de corpo JSON num pedido PUT ou PATCH) de um único modelo | 1 MB | No |
| Modelos | Número de itens devolvidos numa única página | 100 | No |
| Consulta | Número de itens devolvidos numa única página | 100 | Sim |
| Consulta | Número de `AND`  /  `OR` expressões numa consulta | 50 | Sim |
| Consulta | Número de itens de matriz numa `IN`  /  `NOT IN` cláusula | 50 | Sim |
| Consulta | Número de caracteres numa consulta | 8,000 | Sim |
| Consulta | Número de `JOINS` numa consulta | 5 | Yes |

### <a name="rate-limits"></a>Limites de taxa

O quadro seguinte reflete os limites de taxa de diferentes APIs.

| API | Funcionalidade | Limite predefinido | Ajustável? |
| --- | --- | --- | --- |
| Modelos API | Número de pedidos por segundo | 100 | Yes |
| API de Gémeos Digitais | Número de pedidos por segundo | 2.000 | Yes |
| API de Gémeos Digitais | Número de operações de criação/eliminação por segundo em **todos os gémeos e relacionamentos** | 50 | Yes |
| API de Gémeos Digitais | Número de operações de criação/atualização/eliminação por segundo num **único gémeo** ou nas suas relações | 10 | No |
| API de Consulta | Número de pedidos por segundo | 500 | Yes |
| API de Consulta | [Unidades de consulta](../articles/digital-twins/concepts-query-units.md) por segundo | 4000 | Yes |
| Rotas do Evento API | Número de pedidos por segundo | 100 | Yes |

### <a name="other-limits"></a>Outros limites

Os limites dos tipos e campos de dados dentro dos documentos DTDL para os modelos Azure Digital Twins podem ser encontrados dentro da sua documentação de especificação no GitHub: [*Digital Twins Definition Language (DTDL) - versão 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Os detalhes da latência de consulta e outras limitações de consulta podem ser encontrados em [*Como-a-fazer: Consultar o gráfico gémeo*](../articles/digital-twins/how-to-query-graph.md).
