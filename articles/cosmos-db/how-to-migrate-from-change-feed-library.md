---
title: Migrar da biblioteca de processadores de mudanças para o Azure Cosmos DB .NET V3 SDK
description: Saiba como migrar a sua aplicação da utilização da biblioteca do processador de change feed para o Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 7a15e5135cd89d7360a1357e3518b1253e80ee65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019526"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrar da biblioteca de processadores de mudanças para o Azure Cosmos DB .NET V3 SDK

Este artigo descreve as etapas necessárias para migrar o código de uma aplicação existente que utiliza a biblioteca do [processador de change feed](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) para a funcionalidade de feed de [alteração](change-feed.md) na versão mais recente do .NET SDK (também designado por .NET V3 SDK).

## <a name="required-code-changes"></a>Alterações de código exigidas

O .NET V3 SDK tem várias alterações de rutura, são os seguintes passos-chave para migrar a sua aplicação:

1. Converter as `DocumentCollectionInfo` instâncias em `Container` referências para os recipientes monitorizados e locados.
1. As personalizações que utilizam `WithProcessorOptions` devem ser atualizadas para utilização `WithLeaseConfiguration` e para `WithPollInterval` intervalos, `WithStartTime` [para a hora de início,](how-to-configure-change-feed-start-time.md)e `WithMaxItems` para definir a contagem máxima de produto.
1. Coloque o `processorName` on para corresponder ao valor `GetChangeFeedProcessorBuilder` `ChangeFeedProcessorOptions.LeasePrefix` configurado, ou use de outra `string.Empty` forma.
1. As alterações já não são entregues como um `IReadOnlyList<Document>` , em vez disso, é um `IReadOnlyCollection<T>` tipo onde é necessário `T` definir, já não há classe de item base.
1. Para lidar com as alterações, já não precisa de uma implementação, em vez disso, precisa de [definir um delegado](change-feed-processor.md#implementing-the-change-feed-processor). O delegado pode ser uma Função estática ou, se precisar de manter o estado através de execuções, pode criar a sua própria classe e passar um método de instância como delegado.

Por exemplo, se o código original para construir o processador de alimentação de alteração for o seguinte:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

O código migrado será como:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

E o delegado pode ser um método estático:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Estado e recipiente de arrendamento

Semelhante à biblioteca do processador de modificação, a funcionalidade de feed de alteração em .NET V3 SDK utiliza um [recipiente de locação](change-feed-processor.md#components-of-the-change-feed-processor) para armazenar o estado. No entanto, os esquemas são diferentes.

O processador de feed de alteração SDK V3 detetará qualquer estado antigo da biblioteca e migrará-o automaticamente para o novo esquema após a primeira execução do código de aplicação migrado. 

Pode parar com segurança a aplicação utilizando o código antigo, migrar o código para a nova versão, iniciar a aplicação migrada e quaisquer alterações que o tenham ocorrido durante a paragem da aplicação, serão recolhidas e processadas pela nova versão.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Amostras de utilização no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Amostras adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder para saber mais sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do processador de feed de alteração](change-feed-processor.md)
* [Utilizar o calculador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
