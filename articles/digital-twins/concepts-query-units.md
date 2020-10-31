---
title: Unidades de Consulta em Gémeos Digitais Azure
titleSuffix: Azure Digital Twins
description: Compreenda o conceito de faturação de Unidades de Consulta em Gémeos Digitais Azure
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5ba765f03e7ac700fb4338e14358fc55e6423c47
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097044"
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
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a consulta das Gémeas Digitais Azure, visite:
* [*Conceitos: Linguagem de consulta*](concepts-query-language.md)
* [*Como fazer: Consulta do gráfico gémeo*](how-to-query-graph.md)
* [Documentação de referência da API de consulta](/rest/api/digital-twins/dataplane/query/querytwins)

Pode encontrar limites relacionados com a consulta Azure Digital Twins em [*Referência: Limites*](reference-service-limits.md)de serviço .