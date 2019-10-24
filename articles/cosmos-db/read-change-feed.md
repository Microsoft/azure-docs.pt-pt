---
title: Acessando o feed de alterações no Azure Cosmos DB Azure Cosmos DB
description: Este artigo descreve as diferentes opções disponíveis para ler e acessar o feed de alterações no Azure Cosmos DB Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 3d30c9f946f97f06c1a3ba1cd2e77e1ab151a572
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754871"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lendo Azure Cosmos DB feed de alterações

Você pode trabalhar com o Azure Cosmos DB o feed de alterações usando qualquer uma das seguintes opções:

* Usando Azure Functions
* Usando a biblioteca do processador do feed de alterações
* Usando o SDK da API do SQL Azure Cosmos DB

## <a name="using-azure-functions"></a>Usando Azure Functions

Azure Functions é a opção mais simples e recomendada. Ao criar um gatilho de Azure Functions para Cosmos DB, você pode selecionar o contêiner a ser conectado e a função do Azure é disparada sempre que há uma alteração no contêiner. Os gatilhos podem ser criados usando o portal de Azure Functions, o portal de Azure Cosmos DB ou programaticamente com SDKs. O Visual Studio e o VS Code fornecem suporte para escrever Azure Functions, e você pode até mesmo usar a CLI do Azure Functions para o desenvolvimento de plataforma cruzada. Você pode escrever e depurar o código na área de trabalho e, em seguida, implantar a função com um clique. Consulte [computação de banco de dados sem servidor usando Azure Functions](serverless-computing-database.md) e [usando o feed de alterações com Azure Functions](change-feed-functions.md)) artigos para saber mais.

## <a name="using-the-change-feed-processor-library"></a>Usando a biblioteca do processador do feed de alterações

A biblioteca do processador do feed de alterações oculta a complexidade e ainda oferece um controle completo do feed de alterações. A biblioteca segue o padrão observador, em que a função de processamento é chamada pela biblioteca. Se você tiver um feed de alterações de alta taxa de transferência, poderá criar uma instância de vários clientes para ler o feed de alterações. Como você está usando a biblioteca do processador do feed de alterações, ela dividirá automaticamente a carga entre os diferentes clientes sem a necessidade de implementar essa lógica. Toda a complexidade é tratada pela biblioteca. Se você quiser ter seu próprio balanceador de carga, poderá implementar `IPartitionLoadBalancingStrategy` para uma estratégia de partição personalizada para processar o feed de alterações. Para saber mais, consulte [usando a biblioteca do processador do feed de alterações](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Usando o SDK da API do SQL Azure Cosmos DB

Com o SDK, você obtém um controle de baixo nível do feed de alterações. Você pode gerenciar o ponto de verificação, acessar uma chave de partição lógica específica, etc. Se você tiver vários leitores, poderá usar `ChangeFeedOptions` para distribuir a carga de leitura para diferentes threads ou clientes diferentes. 

## <a name="next-steps"></a>Passos seguintes

Agora você pode continuar para saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Usando o feed de alterações com Azure Functions](change-feed-functions.md)
* [Usando a biblioteca do processador do feed de alterações](change-feed-processor.md)
