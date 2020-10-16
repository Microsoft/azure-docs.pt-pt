---
title: Como configurar a hora de início do processador de feed de alteração - Azure Cosmos DB
description: Saiba como configurar o processador de feed de alteração para começar a ler a partir de uma data e hora específicas
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/13/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: dcd92421a7ef4e2314d27a7724be01336c26c9eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018200"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Como configurar a hora de início do processador de alimentação de alteração

Este artigo descreve como pode configurar o [processador de feed de alteração](./change-feed-processor.md) para começar a ler a partir de uma data e hora específicas.

## <a name="default-behavior"></a>Comportamento predefinido

Quando um processador de alimentação de mudança começa pela primeira vez, rubricará o recipiente de locação e iniciará o seu [ciclo de vida de processamento](./change-feed-processor.md#processing-life-cycle). Quaisquer alterações que ocorreram no recipiente antes da inicializada do processador de alimentação de alteração pela primeira vez não serão detetadas.

## <a name="reading-from-a-previous-date-and-time"></a>Leitura a partir de uma data e hora anteriores

É possível inicializar o processador de feed de alteração para ler alterações a partir de uma **data e hora específicas,** passando uma instância de a para `DateTime` a extensão do `WithStartTime` construtor:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

O processador de feed de alteração será inicializado para essa data e hora específicas e começará a ler as alterações que ocorreram depois.

## <a name="reading-from-the-beginning"></a>Leitura desde o início

Noutros cenários, como a migração de dados ou a análise de toda a história de um contentor, precisamos de ler a alteração da alimentação a partir do **início da vida desse contentor.** Para isso, podemos usar `WithStartTime` na extensão do construtor, mas passando, `DateTime.MinValue.ToUniversalTime()` o que geraria a representação UTC do `DateTime` valor mínimo, assim:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

O processador de alimentação de alteração será inicializado e começará a ler alterações a partir do início da vida útil do recipiente.

> [!NOTE]
> Estas opções de personalização apenas funcionam para configurar o ponto de partida no tempo do processador de feed de alteração. Uma vez que o recipiente de arrendamento é inicializado pela primeira vez, mudá-los não tem efeito.

> [!NOTE]
> Ao especificar um ponto no tempo, apenas serão lidas alterações para os itens que existem atualmente no recipiente. Se um item foi eliminado, o seu histórico no Feed de Alteração também é removido.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Amostras de utilização no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Amostras adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder para saber mais sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do processador de feed de alteração](change-feed-processor.md)
* [Utilizar o calculador do feed de alterações](how-to-use-change-feed-estimator.md)
