---
title: Usar o estimador do feed de alterações-Azure Cosmos DB
description: Saiba como usar o estimador do feed de alterações para analisar o progresso do processador do feed de alterações
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 0023f68400b36b9abd3b9d4a789895e79f67aa03
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092958"
---
# <a name="use-the-change-feed-estimator"></a>Usar o estimador do feed de alterações

Este artigo descreve como você pode monitorar o progresso de suas instâncias do [processador do feed de alterações](./change-feed-processor.md) ao ler o feed de alterações.

## <a name="why-is-monitoring-progress-important"></a>Por que o progresso do monitoramento é importante?

O processador do feed de alterações atua como um ponteiro que avança em seu [feed de alterações](./change-feed.md) e entrega as alterações a uma implementação de delegado. 

A implantação do processador do feed de alterações pode processar alterações a uma taxa específica com base em seus recursos disponíveis, como CPU, memória, rede e assim por diante.

Se essa taxa for mais lenta do que a taxa na qual as alterações ocorrem no contêiner Cosmos do Azure, o processador começará a ficar atrasado.

Identificar esse cenário ajuda a entender se precisamos dimensionar nossa implantação do processador do feed de alterações.

## <a name="implement-the-change-feed-estimator"></a>Implementar o estimador do feed de alterações

Assim como o [processador do feed de alterações](./change-feed-processor.md), o estimador do feed de alterações funciona como um modelo de push. O estimador medirá a diferença entre o último item processado (definido pelo estado do contêiner de concessões) e a alteração mais recente no contêiner e enviará por push esse valor a um delegado. O intervalo no qual a medição é realizada também pode ser personalizado com um valor padrão de 5 segundos.

Por exemplo, se o processador do feed de alterações for definido da seguinte maneira:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

A maneira correta de inicializar um estimador para medir esse processador seria o `GetChangeFeedEstimatorBuilder` uso desta forma:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Onde o processador e o estimador compartilham o mesmo `leaseContainer` e o mesmo nome.

Os outros dois parâmetros são o delegado, que receberá um número que representa **quantas alterações estão pendentes para serem lidas** pelo processador e o intervalo de tempo no qual você deseja que essa medida seja executada.

Um exemplo de um delegado que recebe a estimativa é:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Você pode enviar essa estimativa para sua solução de monitoramento e usá-la para entender como seu progresso está se comportando ao longo do tempo.

> [!NOTE]
> O estimador do feed de alterações não precisa ser implantado como parte do processador do feed de alterações, nem fazer parte do mesmo projeto. Ele pode ser independente e ser executado em uma instância completamente diferente. Ele só precisa usar a mesma configuração de nome e concessão.

## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos de uso no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemplos adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos Seguintes

Agora você pode continuar para saber mais sobre o processador do feed de alterações nos seguintes artigos:

* [Visão geral do processador do feed de alterações](change-feed-processor.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
