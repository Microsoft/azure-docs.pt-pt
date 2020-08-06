---
author: baanders
description: incluir arquivo para limites Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 22b00b41c7fce0af57fd9f92b0f42bbd9412afda
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87771043"
---
### <a name="functional-limits"></a>Limites funcionais

A tabela abaixo lista os limites funcionais de Azure Digital Twins na pré-visualização atual.

| Área | Funcionalidade | Limite predefinido | Ajustável? |
| --- | --- | --- | --- |
| Recurso do Azure | Número de casos de Gémeos Digitais Azure numa região, por subscrição | 10 | Yes |
| Digital Twins | Número de gémeos num caso de Gémeos Digitais Azure | 200,000 | Yes |
| Digital Twins | Número de relações recebidas com um único gémeo | 5000 | No |
| Digital Twins | Número de relações de saída de um único gémeo | 5000 | No |
| Encaminhamento | Número de pontos finais para uma única instância Azure Digital Twins | 6 | No |
| Encaminhamento | Número de rotas para uma única instância Azure Digital Twins | 6 | Yes |
| Modelos | Número de modelos dentro de uma única instância Azure Digital Twins | 10,000 | Yes |
| Modelos | Número de modelos que podem ser carregados numa única chamada API | 250 | No |
| Modelos | Número de itens devolvidos numa única página | 100 | No |
| Consulta | Número de itens devolvidos numa única página | 100 | No |
| Consulta | Número de `AND`  /  `OR` expressões numa consulta | 50 | Sim |
| Consulta | Número de itens de matriz numa `IN`  /  `NOT IN` cláusula | 50 | Sim |
| Consulta | Número de caracteres numa consulta | 8,000 | Sim |
| Consulta | Número de `JOINS` numa consulta | 5 | Yes |

### <a name="rate-limits"></a>Limites de taxa

Esta tabela reflete os limites de taxa de diferentes APIs.

| API | Funcionalidade | Limite predefinido | Ajustável? |
| --- | --- | --- | --- |
| Modelos API | Número de pedidos por segundo | 100 | Yes |
| API de Gémeos Digitais | Número de pedidos por segundo | 1,000 | Yes |
| API de Consulta | Número de pedidos por segundo | 500 | Yes |
| API de Consulta | Unidades de consulta por segundo | 4000 | Yes |
| Rotas do Evento API | Número de pedidos por segundo | 100 | Yes |

### <a name="other-limits"></a>Outros limites

Os limites dos tipos e campos de dados dentro dos documentos DTDL para os modelos Azure Digital Twins podem ser encontrados dentro da sua documentação de especificação no GitHub: [*Digital Twins Definition Language (DTDL) - versão 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Os detalhes da latência de consulta e outras diretrizes sobre consultas de escrita durante a pré-visualização podem ser encontrados em [*Como-a-: Consultar o gráfico gémeo*](../articles/digital-twins/how-to-query-graph.md).