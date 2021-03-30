---
title: Otimizar o custo de armazenamento no Azure Cosmos DB
description: Este artigo explica como gerir os custos de armazenamento dos dados armazenados na Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93080855"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Otimizar o custo de armazenamento no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB oferece armazenamento e produção ilimitados. Ao contrário do rendimento, que tem de providenciar/configurar nos seus contentores ou bases de dados Azure Cosmos, o armazenamento é faturado com base numa base de consumo. Você é cobrado apenas para o armazenamento lógico que você consome e você não tem que reservar qualquer armazenamento com antecedência. O armazenamento escala automaticamente para cima e para baixo com base nos dados que adiciona ou remove a um recipiente Azure Cosmos.

## <a name="storage-cost"></a>Custo do armazenamento

O armazenamento é faturado com a unidade de GBs. O armazenamento com sSD local é utilizado pelos seus dados e pela sua indexação. O armazenamento total utilizado é igual ao armazenamento exigido pelos dados e índices utilizados em todas as regiões onde está a utilizar o Azure Cosmos DB. Se replicar globalmente uma conta Azure Cosmos em três regiões, pagará o custo total de armazenamento em cada uma dessas três regiões. Para estimar o seu requisito de armazenamento, consulte a ferramenta [de planeamento de capacidade.](https://www.documentdb.com/capacityplanner) O custo de armazenamento em Azure Cosmos DB é $0,25 GB/mês, consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as últimas atualizações. Pode configurar alertas para determinar o armazenamento utilizado pelo seu contentor Azure Cosmos, para monitorizar o seu armazenamento, consulte o artigo [monitor Azure Cosmos DB).](./monitor-cosmos-db.md)

## <a name="optimize-cost-with-item-size"></a>Otimizar o custo com o tamanho do item

A Azure Cosmos DB espera que o tamanho do item seja de 2 MB ou menos para um desempenho ótimo e benefícios de custos. Se precisar de qualquer item para armazenar mais de 2 MB de dados, considere redesenhar o esquema do produto. No caso raro de não conseguir redesenhar o esquema, pode dividir o item em subitems e ligá-lo logicamente a um identificador comum(ID). Todas as características do Azure Cosmos funcionam de forma consistente, ancorando-se a esse identificador lógico.

## <a name="optimize-cost-with-indexing"></a>Otimizar o custo com a indexação

Por padrão, os dados são automaticamente indexados, o que pode aumentar o armazenamento total consumido. No entanto, pode aplicar políticas de índice personalizados para reduzir esta sobrecarga. A indexação automática que não foi sintonizada através da política é de cerca de 10-20% do tamanho do item. Ao remover ou personalizar políticas de índice, você não paga um custo extra para as escritas e não requer capacidade adicional de produção. Consulte [indexação em Azure Cosmos DB](index-policy.md) para configurar políticas de indexação personalizadas. Se já trabalhou com bases de dados relacionais antes, pode pensar que "indexar tudo" significa duplicar o armazenamento ou mais. No entanto, em Azure Cosmos DB, no caso mediano, é muito mais baixo. Em Azure Cosmos DB, a sobrecarga de armazenamento do índice é tipicamente baixa (10-20%) mesmo com indexação automática, porque é projetado para uma baixa pegada de armazenamento. Ao gerir a política de indexação, pode controlar a compensação da pegada de índice e o desempenho da consulta de uma forma mais fina.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Otimizar o custo com o tempo para viver e mudar o feed

Uma vez que já não precise dos dados, pode eliminá-lo graciosamente da sua conta Azure Cosmos utilizando [o tempo para viver,](time-to-live.md) [altere](change-feed.md) o feed ou pode migrar os dados antigos para outra loja de dados, como o armazenamento de blob Azure ou o armazém de dados Azure. Com o tempo de vida ou TTL, a Azure Cosmos DB fornece a capacidade de eliminar automaticamente os itens de um recipiente após um determinado período de tempo. Por predefinição, pode definir a hora para viver ao nível do recipiente e sobrepor o valor numa base por item. Depois de definir o TTL num recipiente ou a um nível de item, a Azure Cosmos DB removerá automaticamente estes itens após o período de tempo desde a última modificação. Ao utilizar o feed de alteração, pode migrar dados para outro recipiente em Azure Cosmos DB ou para uma loja de dados externa. A migração leva zero tempo de inguca e quando terminar a migração, pode apagar ou configurar o tempo de vida para apagar o recipiente Azure Cosmos de origem.

## <a name="optimize-cost-with-rich-media-data-types"></a>Otimizar o custo com tipos de dados de mídia rica 

Se quiser armazenar tipos de mídia ricos, por exemplo, vídeos, imagens, etc., tem várias opções no Azure Cosmos DB. Uma opção é armazenar estes tipos de mídia ricos como itens Azure Cosmos. Existe um limite de 2-MB por item, e pode evitar este limite acorr o item de dados em vários subíndes. Ou pode armazená-los no armazenamento da Azure Blob e utilizar os metadados para os fazer referência aos seus itens Azure Cosmos. Há uma série de prós e contras com esta abordagem. A primeira abordagem dá-lhe o melhor desempenho em termos de latência, através de SLAs mais capacidades de distribuição global chave na mão para os ricos tipos de dados de mídia, além dos seus itens regulares da Azure Cosmos. No entanto, o apoio está disponível a um preço mais elevado. Ao armazenar meios de comunicação no armazenamento da Azure Blob, pode baixar os seus custos globais. Se a latência for crítica, poderá utilizar o armazenamento premium para ficheiros de mídia ricos que são referenciados a partir de itens da Azure Cosmos. Isto integra-se de forma nativa com a CDN para servir imagens do servidor edge a um custo mais baixo para contornar a geo-restrição. O lado positivo deste cenário é que você tem que lidar com dois serviços - Azure Cosmos DB e Azure Blob armazenamento, que podem aumentar os custos operacionais. 

## <a name="check-storage-consumed"></a>Verifique o armazenamento consumido

Para verificar o consumo de armazenamento de um recipiente Azure Cosmos, pode executar um pedido DE CABEÇA ou GET no recipiente e inspecionar os `x-ms-request-quota` `x-ms-request-usage` cabeçalhos e os cabeçalhos. Em alternativa, ao trabalhar com o .NET SDK, pode utilizar as propriedades [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100))e [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) para obter o armazenamento consumido.

## <a name="using-sdk"></a>Utilizar o SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder para saber mais sobre a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas da Azure Cosmos em várias regiões](optimize-cost-regions.md)
