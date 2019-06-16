---
title: Níveis de desempenho do Azure Cosmos DB extintos
description: Saiba mais sobre níveis de desempenho S1, S2 e S3 anteriormente disponíveis no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 06fa98ae4acc2252d8866858ed0e2194ed84ff79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60928291"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Extinguir os níveis de desempenho S1, S2 e S3

> [!IMPORTANT] 
> Os níveis de desempenho S1, S2 e S3 discutidos neste artigo estão a ser descontinuados e já não estão disponíveis para as novas contas do Azure Cosmos DB.
>

Este artigo fornece uma descrição geral dos níveis de desempenho S1, S2 e S3 e discute como as coleções que utilizam estes níveis de desempenho podem ser migradas única para coleções particionadas. Depois de ler este artigo, será capaz de responder às seguintes perguntas:

- [Por que são os níveis de desempenho S1, S2 e S3 está a ser descontinuado?](#why-retired)
- [Como coleções de partição única e coleções particionadas compara para S1, S2, S3 níveis de desempenho?](#compare)
- [O que preciso fazer para garantir um acesso ininterrupto aos meus dados?](#uninterrupted-access)
- [Como irá alterar a minha coleção após a migração?](#collection-change)
- [Como os minha faturação será alterado depois de eu estou a migrar para coleções de partição única?](#billing-change)
- [E se eu precisar de mais de 10 GB de armazenamento?](#more-storage-needed)
- [Posso alterar entre o S1, S2 e S3 níveis de desempenho antes da migração planeada?](#change-before)
- [Como posso migrar de S1, S2, S3 níveis de desempenho para coleções de partição única no meu próprio?](#migrate-diy)
- [Como estou eu afetado se eu for um cliente EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Por que motivo está o desempenho S1, S2 e S3 níveis descontinuado?

Os níveis de desempenho S1, S2 e S3 oferece a flexibilidade que a oferta padrão do Azure Cosmos DB oferece. Com o S1, S2, S3 níveis de desempenho, capacidade de débito e armazenamento previamente foram definidas e não ofereciam a elasticidade. O Azure Cosmos DB oferece a capacidade de personalizar o seu débito e armazenamento, oferecendo a muito mais flexibilidade em sua capacidade de dimensionar conforme suas necessidades mudam.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Como coleções de partição única e coleções particionadas compara para S1, S2, S3 níveis de desempenho?

A tabela seguinte compara as opções de armazenamento e débito disponíveis em coleções de partição única, as coleções particionadas e S1, S2, níveis de desempenho S3. Eis um exemplo para a região E.u.a. Leste 2:

|   |Coleção particionada|Coleção de partição única|S1|S2|S3|
|---|---|---|---|---|---|
|Débito máximo|Ilimitado|10K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Débito mínimo|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Armazenamento máximo|Ilimitado|10 GB|10 GB|10 GB|10 GB|
|Preço (mensal)|Débito: us $6 / 100 RU/s<br><br>Armazenamento: US $ 0,25/GB|Débito: us $6 / 100 RU/s<br><br>Armazenamento: US $ 0,25/GB|$25 USD|US $50 USD|US $100 USD|

Está num cliente EA? Se assim for, veja [qual estou eu o impacto se eu for um cliente EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>O que preciso fazer para garantir um acesso ininterrupto aos meus dados?

Se tiver uma coleção S1, S2 ou S3, deve migrar a coleção para uma coleção de partição única por meio de programação [com o SDK de .NET](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Como irá alterar a minha coleção após a migração?

Se tiver uma coleção S1, pode migrá-los para uma coleção de partições individual com débito de 400 RU/s. 400 RU/s é o débito mais baixo disponível com coleções de partição única. No entanto, o custo de 400 RU/s numa coleção de partição única é aproximadamente o mesmo que estava prestando com sua coleção de S1 e 250 RU/s – para que não é pagando para o extra 150 RU/s disponíveis para si.

Se tiver uma coleção de S2, pode migrá-los para uma coleção de partição única com 1 mil RU/s. Não verá nenhuma alteração para o seu nível de débito.

Se tiver uma coleção S3, pode migrá-los para uma coleção de partição única com 2,5 mil RU/s. Não verá nenhuma alteração para o seu nível de débito.

Em cada um desses casos, após a migração da coleção, será capaz de personalizar seu nível de débito ou dimensioná-lo e reduzir verticalmente conforme necessário para fornecer acesso de latência baixa aos seus utilizadores. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Como os minha faturação será alterado depois de eu migrar para as coleções de partição única?

Supondo que tiver 10 coleções de S1, 1 GB de armazenamento para cada um, na região E.u.a. leste, e é possível migrar esses 10 coleções de S1 para coleções de partição única 10 em 400 RU/seg (o nível mínimo). A fatura será semelhante ao seguinte se mantiver as 10 coleções de partição única para um mês completo:

![Como de preço S1 para 10 coleções se compara com 10 coleções com preços de uma coleção de partição única](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>E se eu precisar de mais de 10 GB de armazenamento?

Se tem uma coleção com o nível de desempenho S1, S2 ou S3, ou uma coleção de partição única, todos os quais tenham 10 GB de armazenamento disponível, que pode utilizar a ferramenta de migração de dados do Azure Cosmos DB para migrar os seus dados para uma coleção particionada com praticamente armazenamento ilimitado. Para informações sobre os benefícios de uma coleção particionada, consulte [criação de partições e dimensionamento no Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Posso alterar entre o S1, S2 e S3 níveis de desempenho antes da migração planeada?

Apenas contas existentes com desempenho S1, S2 e S3 pode ser alteradas e alterar os escalões de nível de desempenho por meio de programação [com o SDK de .NET](#migrate-diy). Se alterar do S1, S3 ou S3 para uma coleção de partição única, não pode retornar para os níveis de desempenho S1, S2 ou S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Como posso migrar de S1, S2, S3 níveis de desempenho para coleções de partição única no meu próprio?

Pode migrar entre os níveis de desempenho S1, S2 e S3 para coleções de partição única por meio de programação [com o SDK de .NET](#migrate-diy). Pode fazê-lo por conta própria antes da migração planeada para aproveitar as opções de flexível débito disponíveis com coleções de partição única.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrar para coleções de partição única com o SDK de .NET

Esta secção abrange apenas alterar o desempenho de uma coleção de nível com o [SQL .NET API](sql-api-sdk-dotnet.md), mas o processo é semelhante para nossos outros SDKs.

Este é um trecho de código para alterar o débito da coleção para 5000 unidades de pedido por segundo:
    
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

Visite [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver exemplos adicionais e saber mais sobre métodos nossa oferta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Como estou eu afetado se eu for um cliente EA?

Os clientes com EA será preço protegido até ao fim de seu contrato atual.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre preços e gestão de dados com o Azure Cosmos DB, explore estes recursos:

1.  [A criação de partições de dados no Cosmos DB](sql-api-partition-data.md). Compreenda a diferença entre o contentor de partição única e particionadas contentores, bem como dicas sobre como implementar uma estratégia de particionamento para dimensionar de forma totalmente integrada.
2.  [Preços do cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Saiba mais sobre o custo de aprovisionamento de débito e consumo de armazenamento.
3.  [Unidades de pedido](request-units.md). Compreenda o consumo de débito para os tipos de operação diferente, por exemplo, leitura, escrita, consulta.
