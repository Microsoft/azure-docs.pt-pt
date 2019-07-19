---
title: Como usar Azure Cosmos DB feed de alterações com Azure Functions
description: Usar Azure Cosmos DB feed de alterações com Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: db3f1b6657ae455ae049eaffd6758fc7e6944fb9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001029"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquiteturas baseadas em eventos sem servidor com Azure Cosmos DB e Azure Functions

Azure Functions fornece a maneira mais simples de se conectar ao [feed de alterações](change-feed.md). Você pode criar pequenas Azure Functions reativas que serão disparadas automaticamente em cada novo evento em seu feed de alterações do contêiner Cosmos do Azure.

![Funções baseadas em evento sem servidor trabalhando com o gatilho Azure Cosmos DB](./media/change-feed-functions/functions.png)

Com o [gatilho Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), você pode aproveitar a funcionalidade de detecção de eventos confiáveis e dimensionamento do [processador do feed de alterações](./change-feed-processor.md)sem a necessidade de manter qualquer infraestrutura de [trabalho](./change-feed-processor.md). Concentre-se apenas na lógica da função do Azure sem se preocupar com o restante do pipeline de fornecimento de eventos. Você pode até mesmo misturar o gatilho com quaisquer outras [associações de Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Atualmente, o gatilho Azure Cosmos DB tem suporte para uso somente com a API básica (SQL).

## <a name="requirements"></a>Requisitos

Para implementar um fluxo baseado em eventos sem servidor, você precisa:

* **O contêiner monitorado**: O contêiner monitorado é o contêiner Cosmos do Azure que está sendo monitorado e armazena os dados dos quais o feed de alterações é gerado. As inserções e alterações (por exemplo, CRUD) para o contêiner monitorado são refletidas no feed de alterações do contêiner.
* **O contêiner de concessão**: O contêiner de concessão mantém o estado entre várias e dinâmicas instâncias de função do Azure sem servidor e habilita o dimensionamento dinâmico. Esse contêiner de concessão pode ser criado manualmente ou automaticamente pelo Azure Cosmos DB Trigger.To criar automaticamente o contêiner de concessão, definir o sinalizador *CreateLeaseCollectionIfNotExists* na [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Os contêineres de concessão particionados devem ter `/id` uma definição de chave de partição.

## <a name="create-your-azure-cosmos-db-trigger"></a>Criar seu gatilho de Azure Cosmos DB

A criação de sua função do Azure com um gatilho Azure Cosmos DB agora tem suporte em todas as Azure Functions IDE e integrações da CLI:

* [Extensão do Visual Studio](../azure-functions/functions-develop-vs.md) para usuários do Visual Studio.
* [Extensão principal do Visual Studio](https://code.visualstudio.com/tutorials/functions-extension/create-function) para Visual Studio Code usuários.
* E, finalmente, as [ferramentas da CLI principal](../azure-functions/functions-run-local.md#create-func) para uma experiência independente de IDE de plataforma cruzada.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Executar o gatilho de Azure Cosmos DB localmente

Você pode executar a [função do Azure localmente](../azure-functions/functions-develop-local.md) com o emulador de [Azure Cosmos DB](./local-emulator.md) para criar e desenvolver seus fluxos baseados em evento sem servidor com uma assinatura do Azure ou incorrer em custos.

Se você quiser testar cenários ao vivo na nuvem, poderá [tentar Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem qualquer cartão de crédito ou assinatura do Azure necessária.

## <a name="next-steps"></a>Passos Seguintes

Agora você pode continuar a saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Maneiras de ler o feed de alterações](read-change-feed.md)
* [Usando a alteração de biblioteca processador do feed](change-feed-processor.md)
* [Como trabalhar com a biblioteca do processador do feed de alterações](change-feed-processor.md)
* [Computação de banco de dados sem servidor usando Azure Cosmos DB e Azure Functions](serverless-computing-database.md)
