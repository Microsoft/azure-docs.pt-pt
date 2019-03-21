---
title: Otimizar o custo de armazenamento no Azure Cosmos DB
description: Este artigo explica como gerenciar custos de armazenamento para os dados armazenados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: aed32db257ddbc8cb5b8f4af5f27024c23583798
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892409"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Otimizar o custo de armazenamento no Azure Cosmos DB

O Azure Cosmos DB oferece armazenamento ilimitado e débito. Ao contrário de produtividade, que tem de aprovisionar/configurar nos seus contentores do Azure Cosmos ou bases de dados, o armazenamento é cobrado com base numa base de consumo. É-lhe cobrada apenas para o armazenamento lógico que consumir e não tem de reservar qualquer armazenamento com antecedência. Armazenamento automaticamente aumenta e diminui com base nos dados de que adicionar ou remove para um contentor do Azure Cosmos DB.

## <a name="storage-cost"></a>Custo de armazenamento

O armazenamento é faturado com a unidade de GBs. Armazenamento baseado em SSD local é utilizado pelos seus dados e indexação. O armazenamento total utilizado é igual para o armazenamento necessário para os dados e os índices utilizados em todas as regiões em que estiver a utilizar o Azure Cosmos DB. Se replicar globalmente uma conta do Cosmos do Azure em três regiões, pagará pelo custo de armazenamento total em cada um desses três regiões. Para calcular a seu requisito de armazenamento, consulte [Planeador de capacidade](https://www.documentdb.com/capacityplanner) ferramenta. O custo do armazenamento no Azure Cosmos DB é US $0,25 GB/mês, consulte [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) mais recentes atualizações. Pode configurar alertas para determinar o armazenamento utilizado pelo seu contentor do Cosmos do Azure, para monitorizar o seu armazenamento, consulte [Monitor do Azure Cosmos DB](monitor-accounts.md)) artigo.

## <a name="optimize-cost-with-item-size"></a>Otimizar o custo com o tamanho do item

O Azure Cosmos DB espera que o tamanho do item a ser 2 MB ou menos para otimizar o desempenho e os benefícios de custos. Se precisar de qualquer item para armazenar mais de 2 MB de dados, considere a reestruturar o esquema de item. No evento raro que não é possível recriar o esquema, pode dividir o item em subitems e ligá-las logicamente com uma identifier(ID) comuns. Todas as funcionalidades do Azure Cosmos DB trabalham consistentemente ancoragem para esse identificador lógico.

## <a name="optimize-cost-with-indexing"></a>Otimizar o custo de indexação

Por predefinição, os dados são indexados automaticamente, que pode aumentar o armazenamento total consumido. No entanto, pode aplicar políticas de índice personalizado para reduzir esta sobrecarga. Indexação automática que não foi ajustado por meio da diretiva é cerca de 10 a 20% do tamanho do item. Ao remover ou personalizar as políticas de índice, não tenha despesas extra para escritas de custos e não exigem a capacidade de débito adicionais. Ver [indexação no Azure Cosmos DB](indexing-policies.md) para configurar políticas de indexação personalizadas. Se já trabalhou com bases de dados relacionais antes, pode imaginar que "indexar tudo" significa a duplicação de armazenamento ou superior. No entanto, no Azure Cosmos DB, no caso do mediano, é muito inferior. No Azure Cosmos DB, a sobrecarga de armazenamento de índice é normalmente baixa (10 a 20%) mesmo com indexação automática, como ele foi projetado para um espaço de armazenamento reduzido. Ao gerir a política de indexação, pode controlar o compromisso do desempenho de requisitos de espaço e consulta de índice de uma maneira mais refinada.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Otimizar o custo com a hora em direto e alterar feed

Assim que não precisa mais os dados pode normalmente eliminá-la da sua conta do Cosmos do Azure utilizando [TTL](time-to-live.md), [feed de alterações](change-feed.md) ou pode migrar os dados antigos para outro armazenamento de dados, como o armazenamento de Blobs do Azure ou o armazém de dados do Azure. Com o tempo de duração ou o valor de TTL, o Azure Cosmos DB fornece a capacidade de excluir itens automaticamente a partir de um contentor após um determinado período de tempo. Por predefinição, pode definir a hora em direto ao nível do contentor e substituir o valor numa base por item. Depois de definir o valor de TTL um contentor ou num nível de item, Azure Cosmos DB irá automaticamente remover estes itens após o período de tempo desde a hora da que última foram modificados. Ao utilizar o feed de alterações, pode migrar dados para qualquer outro contentor no Azure Cosmos DB ou para um arquivo de dados externo. A migração assume zero período de indisponibilidade e quando é feito a migração, pode eliminar ou configurar o tempo de duração para eliminar o contentor do Cosmos do Azure de origem.

## <a name="optimize-cost-with-rich-media-data-types"></a>Otimizar o custo com tipos de dados de mídia avançada 

Se quiser armazenar tipos de mídia avançada, por exemplo, vídeos, imagens, etc., tem várias opções no Azure Cosmos DB. Uma opção é armazenar esses tipos de mídia avançada como itens do Cosmos do Azure. Existe um limite de 2 MB por item, e pode evitar este limite por meio do encadeamento o item de dados em vários subitems. Ou, pode armazená-los no armazenamento de Blobs do Azure e utilizar os metadados referenciá-los a partir de seus itens do Cosmos do Azure. Há uma série de prós e contras dessa abordagem. A primeira abordagem permite-lhe o melhor desempenho em termos de latência, débito SLAs além de recursos de distribuição global chave na mão para os tipos de dados de mídia avançada, além de seus itens regulares do Cosmos do Azure. No entanto o suporte está disponível por um preço mais elevado. Ao armazenar o suporte de dados no armazenamento de Blobs do Azure, também seria possível reduzir seus custos globais. Se a latência é essencial, poderia usar o armazenamento premium para os ficheiros de mídia avançada, que são referenciados a partir de itens do Cosmos do Azure. Isso integra-se nativamente com a CDN para fornecer imagens do servidor de borda de custo mais baixo de contornar a restrição da geo. O lado com este cenário é que precisa lidar com dois serviços - Azure Cosmos DB e armazenamento de Blobs do Azure, que pode aumentar os custos operacionais. 

## <a name="check-storage-consumed"></a>Verificar o armazenamento consumido

Para verificar o consumo de armazenamento de um contentor do Cosmos do Azure, pode executar um pedido HEAD ou GET no contentor e inspecionar os `x-ms-request-quota` e o `x-ms-request-usage` cabeçalhos. Em alternativa, ao trabalhar com o SDK de .NET, pode utilizar o [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)), e [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) propriedades para obter o armazenamento consumido.

## <a name="using-sdk"></a>Com o SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode avançar para obter mais informações sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos do Azure de várias regiões](optimize-cost-regions.md)

