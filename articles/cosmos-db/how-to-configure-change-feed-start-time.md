---
title: Como configurar o tempo de início do processador de feed de mudança - Azure Cosmos DB
description: Saiba como configurar o processador de feed de mudança para começar a ler a partir de uma data e hora específicas
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586279"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Como configurar o tempo de início do processador de feed de mudança

Este artigo descreve como pode configurar o processador de feed de [mudança](./change-feed-processor.md) para começar a ler a partir de uma data e hora específicas.

## <a name="default-behavior"></a>Comportamento predefinido

Quando um processador de feed de mudança começar pela primeira vez, irá inicializar o recipiente de arrendamento e iniciar o seu ciclo de [vida de processamento.](./change-feed-processor.md#processing-life-cycle) Quaisquer alterações que ocorreram no recipiente antes de o processador de alimentação de mudanças ter sido inicializado pela primeira vez não serão detetadas.

## <a name="reading-from-a-previous-date-and-time"></a>Leitura de uma data e hora anteriores

É possível inicializar o processador de feed de mudança para ler alterações a `DateTime` partir `WithStartTime` de uma data e **hora específicas,** passando uma instância de a para a extensão do construtor:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

O processador de feed de alteração será inicializado para essa data e hora específicas e começará a ler as alterações que ocorreram depois.

## <a name="reading-from-the-beginning"></a>Leitura desde o início

Noutros cenários, como migrações de dados ou análise de toda a história de um contentor, precisamos de ler a mudança de alimentos desde **o início da vida útil desse contentor.** Para isso, podemos `WithStartTime` usar na extensão `DateTime.MinValue.ToUniversalTime()`do construtor, mas a passagem, que geraria a representação UTC do valor mínimo, `DateTime` como assim:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

O processador de alimentação de mudanças será inicializado e começará a ler alterações desde o início da vida útil do recipiente.

> [!NOTE]
> Estas opções de personalização funcionam apenas para configurar o ponto de partida no momento do processador de feed de mudança. Uma vez que o recipiente de arrendamento é inicializado pela primeira vez, mudá-lo não tem efeito.

> [!NOTE]
> Ao especificar um ponto no tempo, apenas serão lidas alterações para os itens que existem atualmente no recipiente. Se um item foi eliminado, o seu histórico no Change Feed também é removido.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Amostras de utilização no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Amostras adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder a mais informações sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do processador de feed de mudança](change-feed-processor.md)
* [Utilizar o calculador do feed de alterações](how-to-use-change-feed-estimator.md)
