---
title: Aceder à mudança de alimentos no Azure Cosmos DB Azure Cosmos DB
description: Este artigo descreve diferentes opções disponíveis para ler e aceder ao feed de mudanças em Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688121"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Leitura do feed de alterações do Azure Cosmos DB

Pode trabalhar com o feed de mudança de DB Azure Cosmos utilizando qualquer uma das seguintes opções:

* Com as Funções do Azure
* Com a biblioteca do processador do feed de alterações
* Com o SDK da API SQL do Azure Cosmos DB

## <a name="using-azure-functions"></a>Com as Funções do Azure

As Funções Azure são a opção mais simples e recomendada. Quando cria um gatilho de Funções Azure para cosmos DB, pode selecionar o recipiente para ligar e a Função Azure é acionada sempre que houver uma alteração no recipiente. Os gatilhos podem ser criados utilizando o portal Funções Azure, o portal Azure Cosmos DB ou programáticamente com SDKs. O Visual Studio e o VS Code fornecem suporte para escrever Funções Azure, e pode até utilizar as Funções Azure CLI para desenvolvimento de plataformas cruzadas. Pode escrever e depurar o código no seu ambiente de trabalho e, em seguida, implementar a função com um clique. Consulte a computação da base de [dados Serverless utilizando funções Azure](serverless-computing-database.md) e utilizando o feed de [mudança com funções Azure](change-feed-functions.md)) para saber mais.

## <a name="using-the-change-feed-processor-library"></a>Com a biblioteca do processador do feed de alterações

A biblioteca do processador de feed de mudança esconde complexidade e ainda lhe dá um controlo completo do feed de mudança. A biblioteca segue o padrão de observador, onde a sua função de processamento é chamada pela biblioteca. Se tiver um feed de mudança de alta alimentação, pode instantaneamente vários clientes lerem o feed de mudança. Como está a usar a biblioteca de processadores de feed de mudança, irá automaticamente dividir a carga entre os diferentes clientes sem ter de implementar esta lógica. Toda a complexidade é tratada pela biblioteca. Se quiser ter o seu próprio equilibrador `IPartitionLoadBalancingStrategy` de carga, então pode implementar uma estratégia de partição personalizada para processar o feed de mudança. Para saber mais, consulte [a utilização da biblioteca](change-feed-processor.md)de processadores de feed de mudança.

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Com o SDK da API SQL do Azure Cosmos DB

Com o SDK, obtém-se um controlo baixo da alimentação de mudanças. Você pode gerir o ponto de verificação, aceder a uma chave de partição lógica particular, etc. Se tiver vários leitores, pode utilizar `ChangeFeedOptions` para distribuir a carga de leitura para diferentes fios ou clientes diferentes.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade de alimentação de mudanças é surgida como fluxo de mudança em MongoDB API e Consulta com predicado em Cassandra API. Para saber mais sobre os detalhes de implementação da MongoDB API, consulte os [streams Change no Azure Cosmos DB API para MongoDB](mongodb-change-streams.md).

A Nativa Apache Cassandra fornece a captura de dados de mudança (CDC), um mecanismo para sinalizar tabelas específicas para o arquivo, bem como rejeitar escritos para essas tabelas uma vez que um tamanho configurável no disco para o log CDC é alcançado. A funcionalidade de feed de mudança em Azure Cosmos DB API para Cassandra aumenta a capacidade de consultar as mudanças com predicado via CQL. Para saber mais sobre os detalhes da implementação, consulte [change feed no Azure Cosmos DB API para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder a mais informações sobre a mudança de alimentos nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Utilização de feed de mudança com funções azure](change-feed-functions.md)
* [Usando a biblioteca de processadores de feed de mudança](change-feed-processor.md)
