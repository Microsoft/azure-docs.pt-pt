---
title: Otimizar o custo de armazenamento em Azure Cosmos DB
description: Este artigo explica como gerir os custos de armazenamento dos dados armazenados no Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 1508adda761fcba7ba70df3bb212d3eb4e32f242
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72754949"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Otimizar o custo de armazenamento em Azure Cosmos DB

A Azure Cosmos DB oferece armazenamento e entrada ilimitados. Ao contrário da entrada, que tem de fornecer/configurar nos seus contentores ou bases de dados Azure Cosmos, o armazenamento é faturado com base no consumo. Você é cobrado apenas para o armazenamento lógico que você consome e você não tem que reservar qualquer armazenamento com antecedência. O armazenamento aumenta automaticamente para cima e para baixo com base nos dados que adiciona ou remove a um recipiente Azure Cosmos.

## <a name="storage-cost"></a>Custo do armazenamento

O armazenamento é faturado com a unidade de GBs. O armazenamento local apoiado pelo SSD é utilizado pelos seus dados e indexação. O armazenamento total utilizado é igual ao armazenamento exigido pelos dados e índices utilizados em todas as regiões onde está a utilizar o Azure Cosmos DB. Se replicar globalmente uma conta Azure Cosmos em três regiões, pagará o custo total de armazenamento em cada uma dessas três regiões. Para estimar o seu requisito de armazenamento, consulte a ferramenta de planejador de [capacidade.](https://www.documentdb.com/capacityplanner) O custo de armazenamento em Azure Cosmos DB é de $0,25 GB/mês, ver [página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para as últimas atualizações. Pode configurar alertas para determinar o armazenamento utilizado pelo seu recipiente Azure Cosmos, para monitorizar o seu armazenamento, consulte o artigo [monitor Azure Cosmos DB](monitor-accounts.md)).

## <a name="optimize-cost-with-item-size"></a>Otimizar o custo com o tamanho do item

A Azure Cosmos DB espera que o tamanho do artigo seja de 2 MB ou menos para um desempenho ótimo e benefícios de custos. Se precisar de algum item para armazenar mais de 2 MB de dados, considere redesenhar o esquema do itema. No caso raro de não conseguir redesenhar o esquema, pode dividir o artigo em subitens e ligá-los logicamente a um identificador comum (ID). Todo o Azure Cosmos DB apresenta trabalho consistentemente ancorando a esse identificador lógico.

## <a name="optimize-cost-with-indexing"></a>Otimizar o custo com indexação

Por padrão, os dados são automaticamente indexados, o que pode aumentar o armazenamento total consumido. No entanto, pode aplicar políticas de índice personalizadopara reduzir esta sobrecarga. A indexação automática que não foi sintonizada através da política é de cerca de 10-20% do tamanho do item. Ao remover ou personalizar políticas de índice, não paga custos adicionais por escritos e não necessita de capacidade adicional de entrada. Consulte [indexação em Azure Cosmos DB](indexing-policies.md) para configurar políticas de indexação personalizadas. Se já trabalhou com bases de dados relacionais antes, pode pensar que "indexar tudo" significa duplicar o armazenamento ou mais. No entanto, em Azure Cosmos DB, no caso mediano, é muito mais baixo. No Azure Cosmos DB, o armazenamento do índice é tipicamente baixo (10-20%) mesmo com indexação automática, porque é projetado para uma baixa pegada de armazenamento. Ao gerir a política de indexação, pode controlar a compensação da pegada de índice e do desempenho da consulta de uma forma mais fina.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Otimizar o custo com o tempo para viver e mudar de alimentação

Uma vez que já não precisa dos dados, pode eliminá-los graciosamente da sua conta Azure Cosmos usando [o tempo para viver,](time-to-live.md) [alterar o feed](change-feed.md) ou pode migrar os dados antigos para outra loja de dados, como o armazenamento de blob Azure ou armazém de dados Azure. Com tempo para viver ou TTL, o Azure Cosmos DB oferece a capacidade de apagar automaticamente os itens de um recipiente após um determinado período de tempo. Por padrão, pode definir o tempo para viver ao nível do contentor e sobrepor o valor numa base por item. Depois de colocar o TTL num recipiente ou a um nível de item, o Azure Cosmos DB removerá automaticamente estes itens após o período de tempo desde a última vez que foram modificados. Ao utilizar o feed de mudança, pode migrar dados para outro contentor em Azure Cosmos DB ou para uma loja de dados externa. A migração demora zero tempo e quando terminar a migração, pode apagar ou configurar o tempo para viver para apagar o recipiente de origem Azure Cosmos.

## <a name="optimize-cost-with-rich-media-data-types"></a>Otimizar o custo com tipos de dados de mídia ricos 

Se quiser armazenar tipos de mídia ricos, por exemplo, vídeos, imagens, etc., tem várias opções no Azure Cosmos DB. Uma opção é armazenar estes tipos de mídia ricos como itens Azure Cosmos. Existe um limite de 2 MB por item, e pode evitar este limite acorrentando o item de dados em vários subitens. Ou pode armazená-los no armazenamento da Azure Blob e utilizar os metadados para os referir a partir dos seus itens Azure Cosmos. Há uma série de prós e contras com esta abordagem. A primeira abordagem dá-lhe o melhor desempenho em termos de latência, slas de produção e capacidades de distribuição global chave na mão para os tipos de dados de mídia ricos, além dos seus itens regulares do Azure Cosmos. No entanto, o suporte está disponível a um preço mais elevado. Ao armazenar meios de comunicação no armazenamento da Blob Azure, pode baixar os seus custos globais. Se a latência for crítica, você pode usar armazenamento premium para ficheiros de mídia ricos que são referenciados a partir de itens Azure Cosmos. Isto integra-se de forma nativa com a CDN para servir imagens do servidor de borda a um custo mais baixo para contornar a restrição geo-restrição. O lado positivo deste cenário é que você tem que lidar com dois serviços - Azure Cosmos DB e Azure Blob armazenamento, o que pode aumentar os custos operacionais. 

## <a name="check-storage-consumed"></a>Verificar o armazenamento consumido

Para verificar o consumo de armazenamento de um recipiente Azure Cosmos, pode executar `x-ms-request-quota` um `x-ms-request-usage` pedido HEAD ou GET no recipiente e inspecionar os cabeçalhos e os cabeçalhos. Em alternativa, ao trabalhar com o .NET SDK, pode utilizar as propriedades [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))e [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) para obter o armazenamento consumido.

## <a name="using-sdk"></a>Utilizar o SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, poderá proceder a mais informações sobre a otimização de custos em Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas multi-regiões da Azure Cosmos](optimize-cost-regions.md)

