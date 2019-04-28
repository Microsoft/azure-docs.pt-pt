---
title: Aceder ao alterar feed no Azure Cosmos DB do Azure Cosmos DB
description: Este artigo descreve as diferentes opções disponíveis para ler e aceder a alterar feed no Azure Cosmos DB do Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 459ed4b6f16d3cfe5bb792be7f063a1253a3006e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60927805"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Feed de alterações de leitura do Azure Cosmos DB

Pode trabalhar com o feed de alterações do Azure Cosmos DB através de qualquer uma das seguintes opções:

* Com as funções do Azure
* Usando a alteração de biblioteca processador do feed
* Utilizar o SDK do Azure Cosmos DB SQL API

## <a name="using-azure-functions"></a>Com as funções do Azure

As funções do Azure é a opção mais simples e recomendada. Quando cria um acionador do Azure Cosmos DB numa aplicação de funções do Azure, pode selecionar o contentor para se ligar e a função do Azure é acionada sempre que houver uma alteração para o contentor. Acionadores podem ser criados com o portal de funções do Azure, o portal do Azure Cosmos DB ou programaticamente com os SDKs. O Visual Studio e o VS Code fornecem suporte para escrever funções do Azure e pode até mesmo usar a CLI de funções do Azure para desenvolvimento Multiplataforma. Pode escrever e depurar o código no seu ambiente de trabalho e, em seguida, implementar a função com um só clique. Ver [computação de base de dados sem servidor com funções do Azure](serverless-computing-database.md) e [usar alterar feed com as funções do Azure](change-feed-functions.md)) artigos para saber mais.

## <a name="using-the-change-feed-processor-library"></a>Usando a alteração de biblioteca processador do feed

A alteração biblioteca processador do feed oculta a complexidade e ainda lhe oferece um controlo total sobre o feed de alterações. A biblioteca segue o padrão observer, onde a sua função de processamento é chamada pela biblioteca. Se tiver um feed de alterações de alto débito, pode instanciar vários clientes para ler o feed de alterações. Como está usando a biblioteca de processamento de feed de alterações, ele dividirá automaticamente a carga entre os clientes diferentes sem ter de voltar a implementar esta lógica. Toda aquela complexidade é manipulada pela biblioteca. Se deseja ter seu próprio Balanceador de carga, então pode implementar `IPartitionLoadBalancingStrategy` para uma partição personalizada feed estratégia para processar a alteração. Para obter mais informações, consulte [usando a alteração de biblioteca processador do feed](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Utilizar o SDK do Azure Cosmos DB SQL API

Com o SDK, obtém um controlo de nível baixo de feed de alterações. Pode gerir o ponto de verificação, etc de chave, uma partição lógica específica de acesso. Se tiver vários leitores, pode usar `ChangeFeedOptions` distribuam a carga de leitura para diferentes threads ou de diferentes clientes. 

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para saber mais sobre a alteração do feed nos seguintes artigos:

* [Descrição geral do feed de alterações](change-feed.md)
* [Usando a alteração do feed com as funções do Azure](change-feed-functions.md)
* [Usando a alteração de biblioteca processador do feed](change-feed-processor.md)
