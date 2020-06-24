---
title: Limites do serviço de pré-visualização pública
titleSuffix: Azure Digital Twins
description: Gráfico mostrando os limites do serviço Azure Digital Twins durante a pré-visualização pública.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 7847964d77e5dc09d2e09f207c47f9504c48e1db
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729205"
---
# <a name="service-limits-in-public-preview"></a>Limites de serviço na visualização pública

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Estes são os limites de serviço da Azure Digital Twins durante a pré-visualização pública.

> [!NOTE]
> Alguns serviços têm limites ajustáveis, representados nas tabelas abaixo com a coluna *ajustável?* Quando o limite pode ser ajustado, o valor *ajustável* é *Sim*.
>
> Se a sua empresa necessitar de elevar um limite ou quota ajustável acima do limite de incumprimento, pode solicitar recursos adicionais [abrindo um bilhete de apoio.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="functional-limits"></a>Limites funcionais

A tabela abaixo lista os limites funcionais de Azure Digital Twins na pré-visualização atual.

| Área | Funcionalidade | Limite | Ajustável? |
| --- | --- | --- | --- |
| Recurso azul | Número de casos de Gémeos Digitais Azure numa região, por subscrição | 10 | Yes |
| Gémeos digitais | Número de gémeos num caso de Gémeos Digitais Azure | 200,000 | Yes |
| Encaminhamento | Número de pontos finais para uma única instância Azure Digital Twins | 6 | No |
| Encaminhamento | Número de rotas para uma única instância Azure Digital Twins | 6 | Yes |
| Modelos | Número de modelos dentro de uma única instância Azure Digital Twins | 10,000 | Yes |
| Modelos | Número de modelos que podem ser carregados numa única chamada API | 250 | No |
| Modelos | Número de itens devolvidos numa única página | 100 | No |
| Consulta | Número de itens devolvidos numa única página | 100 | No |
| Consulta | Número de `AND`  /  `OR` expressões numa consulta | 50 | Sim |
| Consulta | Número de itens de matriz numa `IN`  /  `NOT IN` cláusula | 50 | Sim |
| Consulta | Número de caracteres numa consulta | 8,000 | Sim |
| Consulta | Número de `JOINS` numa consulta | 1 | Yes |

## <a name="rate-limits"></a>Limites de taxa

Esta tabela reflete os limites de taxa de diferentes APIs.

| API | Funcionalidade | Limite | Ajustável? |
| --- | --- | --- | --- |
| Modelos API | Número de pedidos por segundo | 100 | Yes |
| API de Gémeos Digitais | Número de pedidos por segundo | 1,000 | Yes |
| API de Consulta | Número de pedidos por segundo | 500 | Yes |
| API de Consulta | Unidades de consulta por segundo | 4000 | Yes |
| Rotas do Evento API | Número de pedidos por segundo | 100 | Yes |

## <a name="other-limits"></a>Outros limites

Os limites dos tipos e campos de dados dentro dos documentos DTDL para os modelos Azure Digital Twins podem ser encontrados dentro da sua documentação de especificação no GitHub: [Digital Twins Definition Language (DTDL) - versão 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Os detalhes da latência de consulta e outras diretrizes sobre consultas de escrita durante a pré-visualização podem ser encontrados em [Como-a-: Consultar o gráfico gémeo](how-to-query-graph.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o lançamento atual da Azure Digital Twins na visão geral do serviço:
* [Visão geral: O que é Azure Digital Twins?](overview.md)
