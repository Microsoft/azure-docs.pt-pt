---
title: Unidades de Consulta em Gémeos Digitais Azure
titleSuffix: Azure Digital Twins
description: Compreenda o conceito de faturação de Unidades de Consulta em Gémeos Digitais Azure
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0e1c5f08c4292e4f3dfec448d8bf54d5d5601840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99050503"
---
# <a name="query-units-in-azure-digital-twins"></a>Unidades de Consulta em Gémeos Digitais Azure 

A Azure Digital Twins **Query Unit (QU)** é uma unidade de computação a pedido que é usada para executar as suas [consultas Azure Digital Twins](how-to-query-graph.md) utilizando a [API de Consulta](/rest/api/digital-twins/dataplane/query). 

Retira os recursos do sistema como CPU, IOPS e memória que são necessários para realizar operações de consulta suportadas pela Azure Digital Twins, permitindo-lhe acompanhar o uso em Unidades de Consulta.

A quantidade de Unidades de Consulta consumidas para executar uma consulta é afetada por...

* a complexidade da consulta
* o tamanho do conjunto de resultados (de modo que uma consulta que devolva 10 resultados consumirá mais QUs do que uma consulta de complexidade semelhante que devolve apenas um resultado)

Este artigo explica como compreender as Unidades de Consulta e acompanhar o consumo da Unidade de Consulta.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Encontre o consumo da Unidade de Consulta em Gémeos Digitais Azure

Quando fizer uma consulta utilizando a [API de Consulta](/rest/api/digital-twins/dataplane/query)de Gémeos Digitais Azure, pode examinar o cabeçalho de resposta para rastrear o número de QUs que a consulta consumiu. Procure "consulta-charge" na resposta enviada de Azure Digital Twins.

Os [SDKs](how-to-use-apis-sdks.md) Azure Digital Twins permitem extrair o cabeçalho de carga de consulta da resposta pageable. Esta secção mostra como consultar gémeos digitais e como iterar sobre a resposta pageable para extrair o cabeçalho de carga de consulta. 

O seguinte corte de código demonstra como pode extrair as taxas de consulta incorridas ao ligar para a API de consulta. Itera-se primeiro nas páginas de resposta para aceder ao cabeçalho de carga de consulta e, em seguida, iterates sobre os resultados digitais gémeos em cada página. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a consulta das Gémeas Digitais Azure, visite:

* [*Conceitos: Linguagem de consulta*](concepts-query-language.md)
* [*Como fazer: Consulta do gráfico gémeo*](how-to-query-graph.md)
* [Documentação de referência da API de consulta](/rest/api/digital-twins/dataplane/query/querytwins)

Pode encontrar limites relacionados com a consulta da Azure Digital Twins nos limites de [*serviço da Azure Digital Twins*](reference-service-limits.md).