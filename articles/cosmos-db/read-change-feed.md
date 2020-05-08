---
title: Aceder à mudança de alimentos no Azure Cosmos DB Azure Cosmos DB
description: Este artigo descreve diferentes opções disponíveis para ler e aceder à mudança de alimentos em Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982501"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Leitura do feed de alterações do Azure Cosmos DB

Pode trabalhar com o feed de mudança de DB Azure Cosmos utilizando qualquer uma das seguintes opções:

* Com as Funções do Azure
* Utilização do processador de feed de mudança
* Com o SDK da API SQL do Azure Cosmos DB
* Utilizando o modelo de puxar por reparteção de reparação (pré-visualização)

## <a name="using-azure-functions"></a>Com as Funções do Azure

As Funções Azure são a opção mais simples e recomendada. Quando cria um gatilho de Funções Azure para cosmos DB, pode selecionar o recipiente para ligar e a Função Azure é acionada sempre que houver uma alteração no recipiente. Os gatilhos podem ser criados utilizando o portal Funções Azure, o portal Azure Cosmos DB ou programáticamente com SDKs. O Visual Studio e o VS Code fornecem suporte para escrever Funções Azure, e pode até utilizar as Funções Azure CLI para desenvolvimento de plataformas cruzadas. Pode escrever e depurar o código no seu ambiente de trabalho e, em seguida, implementar a função com um clique. Consulte a computação da base de [dados Serverless utilizando funções Azure](serverless-computing-database.md) e utilizando o feed de [mudança com funções Azure](change-feed-functions.md)) para saber mais.

## <a name="using-the-change-feed-processor"></a>Utilização do processador de feed de mudança

O processador de feed de mudança esconde a complexidade e ainda lhe dá um controlo completo do feed de mudança. A biblioteca segue o padrão de observador, onde a sua função de processamento é chamada pela biblioteca. Se tiver um feed de mudança de alta alimentação, pode instantaneamente vários clientes lerem o feed de mudança. Como está a usar a biblioteca de processadores de feed de mudança, irá automaticamente dividir a carga entre os diferentes clientes sem ter de implementar esta lógica. Toda a complexidade é tratada pela biblioteca. Para saber mais, consulte [a utilização do processador de feed de mudança](change-feed-processor.md). O processador de feed de mudança faz parte do [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Com o SDK da API SQL do Azure Cosmos DB

Com o SDK, obtém-se um controlo baixo da alimentação de mudanças. Você pode gerir o ponto de verificação, aceder a uma chave de partição lógica particular, etc. Se tiver vários leitores, pode utilizar `ChangeFeedOptions` para distribuir a carga de leitura para diferentes fios ou clientes diferentes.

## <a name="using-the-change-feed-pull-model"></a>Utilizando o modelo de puxar por desativação

O [modelo de relomizador de reparação](change-feed-pull-model.md) permite-lhe consumir o feed de mudança ao seu próprio ritmo e paralelizar o processamento de alterações com feedRanges. Um FeedRange abrange uma gama de valores-chave de divisória. Utilizando o modelo de puxar por reparação de alterações, também é fácil processar alterações para uma chave de partição específica.

> [!NOTE]
> O modelo de pull de feed de mudança está atualmente em [pré-visualização apenas no Azure Cosmos DB .NET SDK.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) A pré-visualização ainda não está disponível para outras versões SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade de alimentação de mudanças é surgida como fluxo de mudança em MongoDB API e Consulta com predicado em Cassandra API. Para saber mais sobre os detalhes de implementação da MongoDB API, consulte os [streams Change no Azure Cosmos DB API para MongoDB](mongodb-change-streams.md).

A Nativa Apache Cassandra fornece a captura de dados de mudança (CDC), um mecanismo para sinalizar tabelas específicas para o arquivo, bem como rejeitar escritos para essas tabelas uma vez que um tamanho configurável no disco para o log CDC é alcançado. A funcionalidade de feed de mudança em Azure Cosmos DB API para Cassandra aumenta a capacidade de consultar as mudanças com predicado via CQL. Para saber mais sobre os detalhes da implementação, consulte [change feed no Azure Cosmos DB API para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder a mais informações sobre a mudança de alimentos nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Utilização de feed de mudança com funções azure](change-feed-functions.md)
* [Usando a biblioteca de processadores de feed de mudança](change-feed-processor.md)
