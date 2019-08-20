---
title: Otimizar o custo de armazenamento no Azure Cosmos DB
description: Este artigo explica como gerenciar os custos de armazenamento para os dados armazenados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 2955df266bcf164ce4a155acc5209679eff0ce8a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615018"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Otimizar o custo de armazenamento no Azure Cosmos DB

O Azure Cosmos DB oferece armazenamento e taxa de transferência ilimitados. Ao contrário da taxa de transferência, que você precisa provisionar/configurar em seus contêineres de Cosmos do Azure ou bancos de dados, o armazenamento é cobrado com base em uma base de consumo. Você é cobrado apenas pelo armazenamento lógico que consome e não precisa reservar nenhum armazenamento com antecedência. O armazenamento é dimensionado e reduzido automaticamente com base nos dados que você adiciona ou remove a um contêiner Cosmos do Azure.

## <a name="storage-cost"></a>Custo de armazenamento

O armazenamento é cobrado com a unidade de GBs. O armazenamento com suporte do SSD local é usado pelos seus dados e indexação. O armazenamento total usado é igual ao armazenamento exigido pelos dados e índices usados em todas as regiões em que você está usando Azure Cosmos DB. Se você replicar globalmente uma conta do Azure Cosmos em três regiões, pagará pelo custo total de armazenamento em cada uma dessas três regiões. Para estimar seu requisito de armazenamento, consulte ferramenta planejador de [capacidade](https://www.documentdb.com/capacityplanner) . O custo de armazenamento no Azure Cosmos DB é $0.25 GB/mês, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as atualizações mais recentes. Você pode configurar alertas para determinar o armazenamento usado pelo seu contêiner Cosmos do Azure, para monitorar seu armazenamento, consulte o artigo [Azure Cosmos DB do monitor](monitor-accounts.md)).

## <a name="optimize-cost-with-item-size"></a>Otimizar o custo com o tamanho do item

Azure Cosmos DB espera que o tamanho do item seja 2 MB ou menos para benefícios de desempenho e custo ideais. Se você precisar de qualquer item para armazenar mais de 2 MB de dados, considere recriar o esquema do item. No caso raro em que não é possível recriar o esquema, você pode dividir o item em subitens e vinculá-los logicamente a um identificador comum (ID). Todos os Azure Cosmos DB recursos funcionam consistentemente por meio da ancoragem a esse identificador lógico.

## <a name="optimize-cost-with-indexing"></a>Otimizar o custo com a indexação

Por padrão, os dados são indexados automaticamente, o que pode aumentar o armazenamento total consumido. No entanto, você pode aplicar políticas de índice personalizadas para reduzir essa sobrecarga. A indexação automática que não foi ajustada pela política é de cerca de 10-20% do tamanho do item. Ao remover ou personalizar políticas de índice, você não paga custo adicional para gravações e não requer capacidade adicional de taxa de transferência. Consulte [indexação no Azure Cosmos DB](indexing-policies.md) para configurar políticas de indexação personalizadas. Se você já trabalhou com bancos de dados relacionais antes, talvez ache que "indexe tudo" significa dobrar o armazenamento ou superior. No entanto, em Azure Cosmos DB, no caso mediano, é muito menor. Em Azure Cosmos DB, a sobrecarga de armazenamento do índice normalmente é baixa (10-20%) mesmo com a indexação automática, porque ela foi projetada para um espaço de armazenamento baixo. Ao gerenciar a política de indexação, você pode controlar a compensação da superfície do índice e do desempenho da consulta de maneira mais refinada.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Otimize o custo com o tempo de vida e o feed de alterações

Depois que você não precisar mais dos dados, poderá excluí-los normalmente da sua conta do Azure Cosmos usando a [vida](time-to-live.md)útil, o [feed de alterações](change-feed.md) ou pode migrar os dados antigos para outro armazenamento de dados, como o armazenamento de BLOBs do azure ou o Azure data warehouse. Com a vida útil ou TTL, Azure Cosmos DB fornece a capacidade de excluir itens automaticamente de um contêiner após um determinado período de tempo. Por padrão, você pode definir a vida útil no nível de contêiner e substituir o valor por item. Depois de definir o TTL em um contêiner ou em um nível de item, Azure Cosmos DB removerá automaticamente esses itens após o período de tempo desde a última modificação. Usando o feed de alterações, você pode migrar dados para outro contêiner em Azure Cosmos DB ou para um armazenamento de dados externo. A migração leva um tempo de inatividade e quando você termina de migrar, você pode excluir ou configurar a vida útil para excluir o contêiner de Cosmos de origem do Azure.

## <a name="optimize-cost-with-rich-media-data-types"></a>Otimize o custo com tipos de dados de mídia avançados 

Se você quiser armazenar tipos de mídia avançados, por exemplo, vídeos, imagens, etc., você tem várias opções no Azure Cosmos DB. Uma opção é armazenar esses tipos de mídia avançados como itens de Cosmos do Azure. Há um limite de 2 MB por item, e você pode evitar esse limite ao encadear o item de dados em vários subitens. Ou você pode armazená-los no armazenamento de BLOBs do Azure e usar os metadados para referenciá-los de seus itens Cosmos do Azure. Há uma série de prós e contras com essa abordagem. A primeira abordagem oferece o melhor desempenho em termos de latência, SLAs de taxa de transferência e recursos de distribuição global prontos para os tipos de dados de mídia avançada, além de seus itens de Cosmos do Azure regulares. No entanto, o suporte está disponível a um preço mais alto. Ao armazenar mídia no armazenamento de BLOBs do Azure, você pode reduzir os custos gerais. Se a latência for crítica, você poderá usar o armazenamento Premium para arquivos de mídia avançados que são referenciados de itens Cosmos do Azure. Isso se integra nativamente com a CDN para fornecer imagens do servidor de borda a um custo menor para burlar a restrição geográfica. O lado abaixo desse cenário é que você precisa lidar com dois serviços – Azure Cosmos DB e armazenamento de BLOBs do Azure, o que pode aumentar os custos operacionais. 

## <a name="check-storage-consumed"></a>Verificar armazenamento consumido

Para verificar o consumo de armazenamento de um contêiner Cosmos do Azure, você pode executar uma solicitação Head ou Get no contêiner e inspecionar `x-ms-request-quota` o e `x-ms-request-usage` os cabeçalhos. Como alternativa, ao trabalhar com o SDK do .NET, você pode usar as propriedades [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))e [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) para obter o armazenamento consumido.

## <a name="using-sdk"></a>Usando o SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Passos Seguintes

Em seguida, você pode prosseguir para saber mais sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [entender sua fatura de Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre como [otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre como [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre como [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre como [otimizar o custo de contas do Azure Cosmos de várias regiões](optimize-cost-regions.md)

