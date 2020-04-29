---
title: Use o estimador de alimentação de mudança - Azure Cosmos DB
description: Aprenda a usar o estimador de feed de mudança para analisar o progresso do seu processador de feed de mudança
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 0023f68400b36b9abd3b9d4a789895e79f67aa03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77585293"
---
# <a name="use-the-change-feed-estimator"></a>Utilize o estimador de alimentação de alteração

Este artigo descreve como pode monitorizar o progresso das suas instâncias de processador de feed de [mudança](./change-feed-processor.md) enquanto lêem o feed de mudança.

## <a name="why-is-monitoring-progress-important"></a>Porque é que o acompanhamento dos progressos é importante?

O processador de feed de mudança funciona como um ponteiro que avança através do seu feed de [mudança](./change-feed.md) e entrega as alterações para uma implementação de um delegado. 

A implementação do seu processador de feed de mudança pode processar alterações a uma taxa específica com base nos seus recursos disponíveis, como CPU, memória, rede, e assim por diante.

Se esta tarifa for mais lenta do que a taxa a que as suas alterações ocorrem no seu recipiente Azure Cosmos, o seu processador começará a ficar para trás.

Identificar este cenário ajuda a entender se precisamos de escalar a nossa implementação do processador de feed de mudança.

## <a name="implement-the-change-feed-estimator"></a>Implementar o estimador de alimentação de mudança

Tal como o processador de feed de [mudança,](./change-feed-processor.md)o estimador de alimentação de mudanças funciona como um modelo push. O estimador medirá a diferença entre o último item processado (definido pelo estado do contentor de arrendamento) e a última alteração no contentor, e empurrará este valor para um delegado. O intervalo em que a medição é efetuada também pode ser personalizado com um valor predefinido de 5 segundos.

Como exemplo, se o seu processador de feed de mudança for definido desta forma:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

A forma correta de inicializar um estimador para `GetChangeFeedEstimatorBuilder` medir esse processador estaria a usar assim:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Quando tanto o processador como o `leaseContainer` estimador partilham o mesmo nome e o mesmo nome.

Os outros dois parâmetros são o delegado, que receberá um número que representa **quantas alterações estão pendentes para serem lidas** pelo processador, e o intervalo de tempo em que pretende que esta medição seja feita.

Um exemplo de um delegado que recebe a estimativa é:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Pode enviar esta estimativa para a sua solução de monitorização e usá-la para entender como o seu progresso se está a comportar ao longo do tempo.

> [!NOTE]
> O estimador de feed de mudança não precisa de ser implementado como parte do seu processador de feed de mudança, nem fazer parte do mesmo projeto. Pode ser independente e funcionar num caso completamente diferente. Só precisa usar o mesmo nome e configuração de arrendamento.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Amostras de utilização no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Amostras adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder a mais informações sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do processador de feed de mudança](change-feed-processor.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
