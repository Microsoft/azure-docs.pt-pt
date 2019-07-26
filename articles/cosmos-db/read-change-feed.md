---
title: Acessando o feed de alterações no Azure Cosmos DB Azure Cosmos DB
description: Este artigo descreve as diferentes opções disponíveis para ler e acessar o feed de alterações no Azure Cosmos DB Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: 3d52ba1abc22aae6121ea6a36f943851dfcca7a0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467674"
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

Com o SDK, você obtém um controle de baixo nível do feed de alterações. Você pode gerenciar o ponto de verificação, acessar uma chave de partição lógica específica, etc. Se você tiver vários leitores, poderá usar `ChangeFeedOptions` o para distribuir a carga de leitura para diferentes threads ou clientes diferentes. 

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para saber mais sobre a alteração do feed nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Usando a alteração do feed com as funções do Azure](change-feed-functions.md)
* [Usando a alteração de biblioteca processador do feed](change-feed-processor.md)
