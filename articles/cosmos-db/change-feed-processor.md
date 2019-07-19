---
title: Trabalhando com a biblioteca do processador do feed de alterações no Azure Cosmos DB
description: Usando o Azure Cosmos DB biblioteca do processador do feed de alterações.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 42b7cd8a60e70ab75afc30910c46eb49f1f6d62a
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000941"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processador do feed de alterações no Azure Cosmos DB 

O processador do feed de alterações faz parte do [SDK do Azure Cosmos DB v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Ele simplifica o processo de leitura do feed de alterações e distribui o processamento de eventos em vários consumidores com eficiência.

O principal benefício da biblioteca do processador do feed de alterações é seu comportamento tolerante a falhas que garante uma entrega "pelo menos uma vez" de todos os eventos no feed de alterações.

## <a name="components-of-the-change-feed-processor"></a>Componentes do processador do feed de alterações

Há quatro componentes principais da implementação do processador do feed de alterações: 

1. **O contêiner monitorado:** O contêiner monitorado tem os dados dos quais o feed de alterações é gerado. Todas as inserções e atualizações do contêiner monitorado são refletidas no feed de alterações do contêiner.

1. **O contêiner de concessão:** O contêiner de concessão atua como um armazenamento de estado e coordena o processamento do feed de alterações entre vários trabalhadores. O contêiner de concessão pode ser armazenado na mesma conta que o contêiner monitorado ou em uma conta separada. 

1. **O host:** Um host é uma instância de aplicativo que usa o processador do feed de alterações para escutar alterações. Várias instâncias com a mesma configuração de concessão podem ser executadas em paralelo, mas cada instância deve ter um **nome de instância**diferente. 

1. **O delegado:** O delegado é o código que define o que você, o desenvolvedor, deseja fazer com cada lote de alterações que o processador do feed de alterações lê. 

Para entender ainda mais como esses quatro elementos do processador do feed de alterações funcionam juntos, vejamos um exemplo no diagrama a seguir. O contêiner monitorado armazena documentos e usa ' City ' como a chave de partição. Vemos que os valores de chave de partição são distribuídos em intervalos que contêm itens. Há duas instâncias de host e o processador do feed de alterações está atribuindo diferentes intervalos de valores de chave de partição a cada instância para maximizar a distribuição de computação. Cada intervalo está sendo lido em paralelo e seu progresso é mantido separadamente de outros intervalos no contêiner de concessão.

![Exemplo de processador do feed de alterações](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementando o processador do feed de alterações

O ponto de entrada é sempre o contêiner monitorado, de `Container` uma instância chamada `GetChangeFeedProcessorBuilder`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Onde o primeiro parâmetro é um nome distinto que descreve a meta desse processador e o segundo nome é a implementação de delegado que manipulará as alterações. 

Um exemplo de um delegado seria:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Por fim, você define um nome para essa instância `WithInstanceName` de processador com e qual é o contêiner com `WithLeaseContainer`o qual manter o estado de concessão.

Chamar `Build` fornecerá a instância do processador que você pode iniciar chamando `StartAsync`.

## <a name="processing-life-cycle"></a>Ciclo de vida de processamento

O ciclo de vida normal de uma instância de host é:

1. Leia o feed de alterações.
1. Se não houver nenhuma alteração, suspensão por um período de tempo predefinido (personalizável `WithPollInterval` com no Construtor) e vá para #1.
1. Se houver alterações, envie-as para o **delegado**.
1. Quando o delegado concluir o processamento das alterações **com êxito**, atualize o repositório de concessão com o último ponto processado no tempo e vá para #1.

## <a name="error-handling"></a>Processamento de erros

O processador do feed de alterações é resiliente aos erros de código do usuário. Isso significa que, se sua implementação de representante tiver uma exceção sem tratamento (etapa #4), o processamento de threads que o lote específico de alterações será interrompido e um novo thread será criado. O novo thread verificará qual foi o último ponto no tempo em que o repositório de concessão tem para esse intervalo de valores de chave de partição e reiniciará de lá, enviando efetivamente o mesmo lote de alterações para o delegado. Esse comportamento continuará até que seu delegado processe as alterações corretamente e seja o motivo pelo qual o processador do feed de alterações tem uma garantia "pelo menos uma vez", porque se o código delegado for acionado, ele tentará novamente esse lote.

## <a name="dynamic-scaling"></a>Dimensionamento dinâmico

Conforme mencionado durante a introdução, o processador do feed de alterações pode distribuir a computação automaticamente em várias instâncias. Você pode implantar várias instâncias do seu aplicativo usando o processador do feed de alterações e tirar proveito dela, os únicos requisitos importantes são:

1. Todas as instâncias devem ter a mesma configuração de contêiner de concessão.
1. Todas as instâncias devem ter o mesmo nome de fluxo de trabalho.
1. Cada instância precisa ter um nome de instância diferente (`WithInstanceName`).

Se se aplicarem a essas três condições, o processador do feed de alterações usará um algoritmo de distribuição igual, distribuirá todas as concessões no contêiner de concessão em todas as instâncias em execução e paralelizará a computação. Uma concessão só pode pertencer a uma instância em um determinado momento, portanto, o número máximo de instâncias é igual ao número de concessões.

As instâncias podem aumentar e diminuir, e o processador do feed de alterações ajustará dinamicamente a carga redistribuindo-se de acordo.

## <a name="change-feed-and-provisioned-throughput"></a>Feed de alterações e taxa de transferência provisionada

Você é cobrado por RUs consumido, uma vez que a movimentação de dados dentro e fora dos contêineres Cosmos sempre consome RUs. Você é cobrado por RUs consumido pelo contêiner de concessão.

## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para saber mais sobre a alteração do feed nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Maneiras de ler o feed de alterações](read-change-feed.md)
* [Usando a alteração do feed com as funções do Azure](change-feed-functions.md)
