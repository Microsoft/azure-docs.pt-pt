---
title: Níveis de desempenho do Azure Cosmos DB reformados
description: Saiba mais sobre os níveis de desempenho S1, S2 e S3 anteriormente disponíveis em Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: ce81ce9afa45c93010c457bc292bba037607f96f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020886"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Retiring the S1, S2, and S3 performance levels (Os níveis de desempenho S1, S2 e S3 vão ser preteridos)

> [!IMPORTANT] 
> Os níveis de desempenho S1, S2 e S3 discutidos neste artigo estão a ser reformados e já não estão disponíveis para novas contas DB do Azure Cosmos.

Este artigo fornece uma visão geral dos níveis de desempenho de S1, S2 e S3, e discute como as coleções que usam estes níveis de desempenho podem ser migradas para coleções únicas divididas. Depois de ler este artigo, poderá responder às seguintes perguntas:

- [Porque é que os níveis de desempenho do S1, S2 e S3 estão a ser retirados?](#why-retired)
- [Como é que as coleções de divisórias e as coleções de divisórias se comparam aos níveis de desempenho S1, S2, S3?](#compare)
- [O que preciso de fazer para garantir o acesso ininterrupto aos meus dados?](#uninterrupted-access)
- [Como é que a minha coleção vai mudar depois da migração?](#collection-change)
- [Como é que a minha faturação vai mudar depois de eu ser migrado para coleções de divisórias individuais?](#billing-change)
- [E se eu precisar de mais de 20 GB de armazenamento?](#more-storage-needed)
- [Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planeada?](#change-before)
- [Como posso migrar dos níveis de desempenho S1, S2, S3 para coleções de partição individuais sozinha?](#migrate-diy)
- [Como é que eu sou impactado se sou cliente da EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Porque é que os níveis de desempenho do S1, S2 e S3 estão a ser retirados?

Os níveis de desempenho S1, S2 e S3 não oferecem a flexibilidade que a oferta padrão da Azure Cosmos DB proporciona. Com os níveis de desempenho S1, S2, S3, tanto a capacidade de produção como a capacidade de armazenamento foram pré-definidas e não ofereceram elasticidade. A Azure Cosmos DB oferece agora a capacidade de personalizar a sua produção e armazenamento, oferecendo-lhe muito mais flexibilidade na sua capacidade de escalar à medida que as suas necessidades mudam.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Como é que as coleções de divisórias e as coleções de divisórias se comparam aos níveis de desempenho S1, S2, S3?

A tabela a seguir compara as opções de produção e armazenamento disponíveis em coleções de divisórias individuais, coleções divididas e níveis de desempenho S1, S2, S3. Aqui está um exemplo para a região norte-americana east 2:

| Nome da quota  |Coleção dividida|Coleção única de divisórias|S1|S2|S3|
|---|---|---|---|---|---|
|Débito máximo|Ilimitado|10K RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Produção mínima|2,5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Armazenamento máximo|Ilimitado|20 GB|20 GB|20 GB|20 GB|
|Preço (mensal)|Produção: $6 / 100 RU/s<br><br>Armazenamento: $0.25/GB|Produção: $6 / 100 RU/s<br><br>Armazenamento: $0.25/GB|USD $25|USD $50|USD $100|

É cliente da EA? Se sim, vê [como sou impactado se sou cliente da EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>O que preciso de fazer para garantir o acesso ininterrupto aos meus dados?

Se tiver uma coleção S1, S2 ou S3, deve migrar a coleção para uma única coleção de partição programáticamente [utilizando o .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Como é que a minha coleção vai mudar depois da migração?

Se tiver uma coleção S1, pode emigrá-las para uma única coleção de partição com 400 RU/s. 400 RU/s é a produção mais baixa disponível com coleções de divisórias únicas. No entanto, o custo de 400 RU/s numa única coleção de divisórias é aproximadamente o mesmo que estava a pagar com a sua coleção S1 e 250 RU/s – pelo que não está a pagar os 150 RU/s adicionais disponíveis para si.

Se tiver uma coleção S2, pode emigrá-las para uma única coleção de divisórias com 1 K RU/s. Não verá nenhuma mudança para o seu nível de produção.

Se tiver uma coleção S3, pode emigrá-las para uma única coleção de divisórias com 2,5 K RU/s. Não verá nenhuma mudança para o seu nível de produção.

Em cada um destes casos, depois de migrar a coleção, poderá personalizar o seu nível de produção, ou dimensioná-lo para cima e para baixo, conforme necessário para fornecer acesso de baixa latência aos seus utilizadores. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Como é que a minha faturação vai mudar depois de migrar para as coleções de divisórias?

Assumindo que tem 10 coleções S1, 1 GB de armazenamento para cada um, na região leste dos EUA, e migra estas 10 coleções S1 para 10 coleções de divisórias individuais a 400 RU/seg (o nível mínimo). A sua conta será a seguinte se mantiver as 10 coleções de divisórias durante um mês inteiro:

:::image type="content" source="./media/performance-levels/s1-vs-standard-pricing.png" alt-text="Como os preços do S1 para 10 coleções comparam-se a 10 coleções utilizando preços para uma única coleção de partição" border="false":::

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>E se eu precisar de mais de 20 GB de armazenamento?

Quer tenha uma coleção com nível de desempenho S1, S2 ou S3, ou tenha uma única coleção de divisórias, todas elas com 20 GB de armazenamento disponíveis, pode utilizar a ferramenta de migração de dados DB do Azure Cosmos para migrar os seus dados para uma recolha dividida com armazenamento praticamente ilimitado. Para obter informações sobre os benefícios de uma coleção dividida, consulte [partição e dimensionamento em Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planeada?

Apenas as contas existentes com desempenho S1, S2 e S3 podem ser alteradas e alterar os níveis de nível de desempenho programáticamente [utilizando o .NET SDK](#migrate-diy). Se mudar de S1, S3 ou S3 para uma única coleção de partição, não poderá voltar aos níveis de desempenho S1, S2 ou S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Como posso migrar dos níveis de desempenho S1, S2, S3 para coleções de partição individuais sozinha?

Pode migrar dos níveis de desempenho S1, S2 e S3 para coleções de partição únicas programáticamente [utilizando o .NET SDK](#migrate-diy). Você pode fazê-lo por conta própria antes da migração planeada para beneficiar das opções de produção flexível disponíveis com coleções de divisórias únicas.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrar para coleções de divisórias individuais utilizando o .NET SDK

Esta secção cobre apenas a alteração do nível de desempenho de uma coleção utilizando o [SQL .NET API,](sql-api-sdk-dotnet.md)mas o processo é semelhante para os nossos outros SDKs.

Aqui está um código para alterar a produção para 5.000 unidades de pedido por segundo:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Visite a [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver exemplos adicionais e saiba mais sobre os nossos métodos de oferta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**SubstituirOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Como é que eu sou impactado se sou cliente da EA?

Os clientes da EA estarão protegidos pelo preço até ao final do seu contrato atual.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre preços e gestão de dados com a Azure Cosmos DB, explore estes recursos:

1.  [Dados de partilha em Cosmos DB](sql-api-partition-data.md). Compreenda a diferença entre um único recipiente de partição e recipientes divididos, bem como dicas sobre a implementação de uma estratégia de partição para escalar sem problemas.
2.  [Preços do Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) Saiba mais sobre o custo do fornecimento de produção e armazenamento consumista.
3.  [Unidades de solicitação](request-units.md). Compreenda o consumo de produção para diferentes tipos de operação, por exemplo Ler, Escrever, Consultar.
