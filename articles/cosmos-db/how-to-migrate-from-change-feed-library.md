---
title: Migrar da biblioteca de processadores de feed de mudança para o Azure Cosmos DB .NET V3 SDK
description: Saiba como migrar a sua aplicação desde a utilização da biblioteca de processadores de feed de mudança para o Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588888"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrar da biblioteca de processadores de feed de mudança para o Azure Cosmos DB .NET V3 SDK

Este artigo descreve as etapas necessárias para migrar o código de uma aplicação existente que utiliza a biblioteca do [processador de feed](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) de mudança para a funcionalidade de [alimentação](change-feed.md) de mudanças na versão mais recente do .NET SDK (também referido como .NET V3 SDK).

## <a name="required-code-changes"></a>Alterações de código necessárias

O .NET V3 SDK tem várias alterações de rutura, as seguintes são os passos-chave para migrar a sua aplicação:

1. Converter `DocumentCollectionInfo` as instâncias em `Container` referências para os contentores monitorizados e aluga.
1. As personalizações `WithProcessorOptions` que utilizam devem `WithLeaseConfiguration` `WithPollInterval` ser atualizadas `WithStartTime` para utilização `WithMaxItems` e para intervalos, para a hora de [início,](how-to-configure-change-feed-start-time.md)e para definir a contagem máxima do artigo.
1. `processorName` Desloque o `GetChangeFeedProcessorBuilder` on para `ChangeFeedProcessorOptions.LeasePrefix`combinar `string.Empty` com o valor configurado ou utilize de outra forma.
1. As alterações já não `IReadOnlyList<Document>`são entregues como `IReadOnlyCollection<T>` um `T` , em vez disso, é um tipo onde é um tipo que você precisa definir, não há mais classe de item base.
1. Para lidar com as alterações, já não precisa de uma implementação, em vez disso precisa de [definir um delegado.](change-feed-processor.md#implementing-the-change-feed-processor) O delegado pode ser uma Função Estática ou, se precisar manter o estado através das execuções, pode criar a sua própria classe e passar um método de instância como delegado.

Por exemplo, se o código original para construir o processador de feed de mudança for o seguinte:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

O código migrado vai parecer:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

E o delegado pode ser um método estático:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Contentor do Estado e do arrendamento

À semelhança da biblioteca do processador de feed de mudanças, a funcionalidade de alimentação de mudanças em .NET V3 SDK utiliza um recipiente de [aluguer](change-feed-processor.md#components-of-the-change-feed-processor) para armazenar o estado. No entanto, os esquemas são diferentes.

O processador de reparação sDK V3 detetará qualquer estado antigo da biblioteca e migra-lo-á automaticamente para o novo esquema após a primeira execução do código de aplicação migrado. 

Pode parar com segurança a aplicação utilizando o código antigo, migrar o código para a nova versão, iniciar a aplicação em migração, e quaisquer alterações que ocorreram durante a paragem da aplicação, serão recolhidas e processadas pela nova versão.

> [!NOTE]
> As migrações de aplicações que utilizam a biblioteca para o .NET V3 SDK são de sentido único, uma vez que o Estado (arrendamentos) será migrado para o novo esquema. A migração não é compatível para trás.


## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Amostras de utilização no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Amostras adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder a mais informações sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do processador de feed de mudança](change-feed-processor.md)
* [Utilizar o calculador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
