---
title: Níveis de desempenho do Azure Cosmos DB reformados
description: Conheça os níveis de desempenho S1, S2 e S3 anteriormente disponíveis no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 40735f91e2ca58cc42f723c7993686d92f0e5ff0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623336"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Retiring the S1, S2, and S3 performance levels (Os níveis de desempenho S1, S2 e S3 vão ser preteridos)

> [!IMPORTANT] 
> Os níveis de desempenho S1, S2 e S3 discutidos neste artigo estão a ser retirados e já não estão disponíveis para novas contas Do MBS.
>

Este artigo fornece uma visão geral dos níveis de desempenho S1, S2 e S3, e discute como as coleções que usam estes níveis de desempenho podem ser migradas para coleções únicas divididas. Depois de ler este artigo, poderá responder às seguintes perguntas:

- [Porque é que os níveis de desempenho do S1, S2 e S3 estão a ser retirados?](#why-retired)
- [Como é que coleções de partição única e coleções divididas se comparam aos níveis de desempenho S1, S2, S3?](#compare)
- [O que preciso fazer para garantir o acesso ininterrupto aos meus dados?](#uninterrupted-access)
- [Como é que a minha coleção vai mudar depois da migração?](#collection-change)
- [Como é que a minha faturação vai mudar depois de ser migrado para coleções de partição únicas?](#billing-change)
- [E se eu precisar de mais de 20 GB de armazenamento?](#more-storage-needed)
- [Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planeada?](#change-before)
- [Como migrado dos níveis de desempenho S1, S2, S3 para coleções de partição únicas sozinha?](#migrate-diy)
- [Como é que sou afetado se sou cliente da EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Porque é que os níveis de desempenho s1, S2 e S3 estão a ser retirados?

Os níveis de desempenho S1, S2 e S3 não oferecem a flexibilidade que a oferta padrão Azure Cosmos DB oferece. Com os níveis de desempenho S1, S2, S3, tanto a capacidade de entrada como a capacidade de armazenamento foram pré-definidas e não ofereceram elasticidade. A Azure Cosmos DB oferece agora a capacidade de personalizar a sua entrada e armazenamento, oferecendo-lhe muito mais flexibilidade na sua capacidade de escalar à medida que as suas necessidades mudam.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Como é que coleções de partição única e coleções divididas se comparam aos níveis de desempenho S1, S2, S3?

A tabela seguinte compara as opções de entrada e armazenamento disponíveis em coleções de partição únicas, coleções divididas e níveis de desempenho S1, S2, S3. Aqui está um exemplo para a região dos EUA East 2:

|   |Coleção dividida|Coleção única de partição|S1|S2|S3|
|---|---|---|---|---|---|
|Débito máximo|Ilimitado|10K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Rendimento mínimo|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Armazenamento máximo|Ilimitado|20 GB|20 GB|20 GB|20 GB|
|Preço (mensal)|Entrada: $6 / 100 RU/s<br><br>Armazenamento: $0.25/GB|Entrada: $6 / 100 RU/s<br><br>Armazenamento: $0.25/GB|$25 USD|$50 USD|$100 USD|

É cliente da EA? Se sim, vê [como sou impactado se sou um cliente da EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>O que preciso fazer para garantir o acesso ininterrupto aos meus dados?

Se tiver uma coleção S1, S2 ou S3, deverá migrar a coleção para uma única coleção de partições programáticamente [utilizando o .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Como é que a minha coleção vai mudar depois da migração?

Se tiver uma coleção S1, pode emigrar para uma única coleção de partição com 400 RU/s. 400 RU/s é o menor de entrada disponível com coleções de partição únicas. No entanto, o custo de 400 RU/s numa única coleção de partições é aproximadamente o mesmo que estava a pagar com a sua coleção S1 e 250 RU/s – por isso não está a pagar os 150 RU/s extra disponíveis para si.

Se tiver uma coleção S2, pode emigrar para uma única coleção de partição com 1 K RU/s. Não verá alterações no seu nível de entrada.

Se tiver uma coleção S3, pode emigrar para uma única coleção de partição com 2,5 K RU/s. Não verá alterações no seu nível de entrada.

Em cada um destes casos, depois de migrar a coleção, poderá personalizar o seu nível de entrada, ou escaloná-lo para cima e para baixo, conforme necessário para fornecer acesso de baixa latência aos seus utilizadores. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Como é que a minha faturação vai mudar depois de ter migrado para as coleções de partição únicas?

Assumindo que tem 10 coleções S1, 1 GB de armazenamento para cada, na região leste dos EUA, e migra estas 10 coleções S1 para 10 coleções de partição únicas a 400 RU/seg (o nível mínimo). A sua conta será a seguinte se mantiver as 10 coleções de partição únicas por um mês inteiro:

![Como o preço S1 para 10 coleções compara com 10 coleções usando preços para uma única coleção de partição](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>E se eu precisar de mais de 20 GB de armazenamento?

Quer tenha uma coleção com nível de desempenho S1, S2 ou S3, ou tenha uma única coleção de partições, todas elas com 20 GB de armazenamento disponíveis, pode utilizar a ferramenta Azure Cosmos DB Data Migration para migrar os seus dados para uma coleção dividida com virtualmente armazenamento ilimitado. Para obter informações sobre os benefícios de uma coleção dividida, consulte [a partilha e a escala em Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planeada?

Apenas as contas existentes com desempenho S1, S2 e S3 podem ser alteradas e alterar os níveis de desempenho programáticamente [utilizando o .NET SDK](#migrate-diy). Se mudar de S1, S3 ou S3 para uma única coleção de partição, não pode voltar aos níveis de desempenho S1, S2 ou S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Como migrado dos níveis de desempenho S1, S2, S3 para coleções de partição únicas sozinha?

Pode migrar dos níveis de desempenho S1, S2 e S3 para coleções de partição únicas programáticamente [utilizando o .NET SDK](#migrate-diy). Pode fazê-lo por conta própria antes da migração planeada para beneficiar das opções flexíveis de produção disponíveis com coleções de partição únicas.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrar para coleções de partição únicautilizando o .NET SDK

Esta secção abrange apenas a alteração do nível de desempenho de uma coleção utilizando o [SQL .NET API,](sql-api-sdk-dotnet.md)mas o processo é semelhante para os nossos outros SDKs.

Aqui está um código para alterar o reputado da coleção para 5.000 unidades de pedido por segundo:
    
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

Visite a [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver exemplos adicionais e saber mais sobre os nossos métodos de oferta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**Substitua offerasync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Como é que sou afetado se sou cliente da EA?

Os clientes da EA serão protegidos pelo preço até ao final do seu contrato atual.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre preços e gestão de dados com o Azure Cosmos DB, explore estes recursos:

1.  [Dados de partilha em Cosmos DB](sql-api-partition-data.md). Compreenda a diferença entre um único recipiente de partição e recipientes divididos, bem como dicas para implementar uma estratégia de partição para escalar perfeitamente.
2.  [Preços do Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) Saiba mais sobre o custo do fornecimento de entrada e armazenamento de consumo.
3.  [Unidades de pedido](request-units.md). Compreenda o consumo de entrada para diferentes tipos de operação, por exemplo Ler, Escrever, Consultar.
