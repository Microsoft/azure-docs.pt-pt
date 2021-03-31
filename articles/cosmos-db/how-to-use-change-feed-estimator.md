---
title: Use o estimador de alimentação de mudança - Azure Cosmos DB
description: Saiba como usar o estimador de feed de alteração para analisar o progresso do seu processador de feed de mudança
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a44557d15f437317c2b5fa659ab8d4ca3c208edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339845"
---
# <a name="use-the-change-feed-estimator"></a>Utilize o estimador de alimentos para alterações
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como pode monitorizar o progresso das instâncias do processador de [change feed](./change-feed-processor.md) à medida que lê o feed de alteração.

## <a name="why-is-monitoring-progress-important"></a>Porque é que o acompanhamento dos progressos é importante?

O processador de feed de alteração funciona como um ponteiro que avança através do seu [feed de mudança](./change-feed.md) e entrega as alterações a uma implementação de delegado. 

A sua implementação do processador de feed de alteração pode processar alterações a uma determinada taxa com base nos recursos disponíveis, como CPU, memória, rede, e assim por diante.

Se esta taxa for mais lenta do que a velocidade a que as suas alterações ocorrem no seu contentor Azure Cosmos, o seu processador começará a ficar para trás.

Identificar este cenário ajuda a entender se precisamos de escalar a nossa implementação do processador de feed de alteração.

## <a name="implement-the-change-feed-estimator"></a>Implementar o estimador de alimentação de alteração

Tal como o [processador change feed,](./change-feed-processor.md)o estimador de alimentação de alteração funciona como um modelo de push. O estimador medirá a diferença entre o último item processado (definido pelo estado do contentor de locações) e a última alteração no contentor, e empurrará este valor para um delegado. O intervalo em que a medição é efetuada também pode ser personalizado com um valor predefinido de 5 segundos.

Como exemplo, se o seu processador de feed de mudança for definido assim:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

A forma correta de inicializar um estimador para medir esse processador seria utilizar `GetChangeFeedEstimatorBuilder` assim:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Onde tanto o processador como o estimador partilham o mesmo `leaseContainer` e o mesmo nome.

Os outros dois parâmetros são o delegado, que receberá um número que representa **quantas alterações estão pendentes para serem lidas** pelo processador, e o intervalo de tempo em que deseja que esta medição seja feita.

Um exemplo de um delegado que recebe a estimativa é:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Pode enviar esta estimativa para a sua solução de monitorização e usá-la para entender como o seu progresso se está a comportar ao longo do tempo.

> [!NOTE]
> O estimador de feed de alteração não precisa de ser implementado como parte do seu processador de feed de mudança, nem fazer parte do mesmo projeto. Pode ser independente e funcionar num caso completamente diferente. Só precisa de usar o mesmo nome e configuração de arrendamento.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Amostras de utilização no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Amostras adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder para saber mais sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do processador de feed de alteração](change-feed-processor.md)
* [Hora de início do processador do feed de alterações](./change-feed-processor.md#starting-time)