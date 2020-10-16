---
author: baanders
description: incluir arquivo para limites Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 60a5f62d4ea23db1052b2e40d10775dfaa33c632
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91989421"
---
### <a name="functional-limits"></a>Limites funcionais

A tabela abaixo lista os limites funcionais de Azure Digital Twins na pré-visualização atual.

| Área | Funcionalidade | Limite predefinido | Ajustável? |
| --- | --- | --- | --- |
| Recurso do Azure | Número de casos de Gémeos Digitais Azure numa região, por subscrição | 10 | Sim |
| Digital Twins | Número de gémeos num caso de Gémeos Digitais Azure | 200,000 | Sim |
| Digital Twins | Número de relações recebidas com um único gémeo | 5000 | Não |
| Digital Twins | Número de relações de saída de um único gémeo | 5000 | Não |
| Digital Twins | Tamanho máximo de um único gémeo | 32 KB | Não |
| Gémeos digitais API | Tamanho máximo da carga útil do pedido | 32 KB | Não | 
| Encaminhamento | Número de pontos finais para uma única instância Azure Digital Twins | 6 | Não |
| Encaminhamento | Número de rotas para uma única instância Azure Digital Twins | 6 | Sim |
| Modelos | Número de modelos dentro de uma única instância Azure Digital Twins | 10,000 | Sim |
| Modelos | Número de modelos que podem ser carregados numa única chamada API | 250 | Não |
| Modelos | Número de itens devolvidos numa única página | 100 | Não |
| Consulta | Número de itens devolvidos numa única página | 100 | Não |
| Consulta | Número de `AND`  /  `OR` expressões numa consulta | 50 | Sim |
| Consulta | Número de itens de matriz numa `IN`  /  `NOT IN` cláusula | 50 | Sim |
| Consulta | Número de caracteres numa consulta | 8,000 | Sim |
| Consulta | Número de `JOINS` numa consulta | 5 | Sim |

### <a name="rate-limits"></a>Limites de taxa

Esta tabela reflete os limites de taxa de diferentes APIs.

| API | Funcionalidade | Limite predefinido | Ajustável? |
| --- | --- | --- | --- |
| Modelos API | Número de pedidos por segundo | 100 | Sim |
| API de Gémeos Digitais | Número de pedidos por segundo | 1,000 | Sim |
| API de Consulta | Número de pedidos por segundo | 500 | Sim |
| API de Consulta | [Unidades de consulta](../articles/digital-twins/concepts-query-units.md) por segundo | 4000 | Sim |
| Rotas do Evento API | Número de pedidos por segundo | 100 | Sim |

### <a name="other-limits"></a>Outros limites

Os limites dos tipos e campos de dados dentro dos documentos DTDL para os modelos Azure Digital Twins podem ser encontrados dentro da sua documentação de especificação no GitHub: [*Digital Twins Definition Language (DTDL) - versão 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Os detalhes da latência de consulta e outras diretrizes sobre consultas de escrita durante a pré-visualização podem ser encontrados em [*Como-a-: Consultar o gráfico gémeo*](../articles/digital-twins/how-to-query-graph.md).