---
title: Como utilizar o Azure Cosmos DB alterar feed com as funções do Azure
description: Utilização que do Azure Cosmos DB alterar feed com as funções do Azure
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 08429ca76823b9e6c80a197cc390a5964c4198e6
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969006"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquiteturas sem servidor com base em eventos com o Azure Cosmos DB e as funções do Azure

As funções do Azure fornece a forma mais simples de ligar para o [feed de alterações](change-feed.md). Pode criar pequenos reativo as funções do Azure que será automaticamente acionada em cada novo evento no feed de alterações do seu contentor Cosmos do Azure.

![Funções sem servidor com base em eventos trabalhar com o acionador do Azure Cosmos DB](./media/change-feed-functions/functions.png)

Com o [acionador do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), pode tirar partido da [processador do Feed de alterações](./change-feed-processor.md)do dimensionamento e a funcionalidade de deteção de eventos fiável sem a necessidade de manter qualquer [trabalho infraestrutura](./change-feed-processor.md#implementing-the-change-feed-processor-library). Se concentrar apenas na lógica de sua função do Azure sem se preocupar sobre o resto do pipeline de origem do evento. Pode até mesmo combinar o acionador com qualquer outro [enlaces de funções do Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Atualmente, o acionador do Azure Cosmos DB é suportado para utilização com o Core (SQL) API apenas.

## <a name="requirements"></a>Requisitos

Para implementar um fluxo com base em eventos sem servidor, terá de:

* **O contentor monitorizado**: O contentor monitorizado é o contentor do Cosmos do Azure a ser monitorizado e armazena os dados a partir do qual o feed de alterações é gerado. Qualquer inserções e as alterações (por exemplo, CRUD) para o contentor monitorizado são refletidas no feed de alterações do contentor.
* **O contentor da concessão**: O contentor da concessão mantém o estado em vários e função de Azure sem servidor dinâmica instâncias e permite o dimensionamento dinâmico. Este contentor de concessão pode ser manual ou automaticamente criada pelo Trigger.To do Azure Cosmos DB automaticamente criar o contentor da concessão, defina o *CreateLeaseCollectionIfNotExists* sinalizar no [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Contentores de concessão particionadas são necessários para ter um `/id` definição da chave de partição.

## <a name="create-your-azure-cosmos-db-trigger"></a>Criar o acionador do Azure Cosmos DB

A criação de sua função do Azure com um acionador do Azure Cosmos DB é agora suportada em todos os IDE de funções do Azure e integrações de CLI:

* [Extensão do Visual Studio](../azure-functions/functions-develop-vs.md) usuários do Visual Studio.
* [Extensão do Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) para usuários do Visual Studio Code.
* E finalmente [ferramentas de CLI principais](../azure-functions/functions-run-local.md#create-func) para uma experiência de agnóstica de IDE para várias plataformas.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Executar localmente o acionador do Azure Cosmos DB

Pode executar sua [localmente a função do Azure](../azure-functions/functions-develop-local.md) com o [emulador do Azure Cosmos DB](./local-emulator.md) para criar e desenvolver os seus fluxos com base em eventos sem servidor sem uma subscrição do Azure ou incorrer em custos.

Se pretender testar cenários em direto na cloud, pode [experimentar o Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem qualquer cartão de crédito ou é necessária uma subscrição do Azure.

## <a name="next-steps"></a>Passos Seguintes

Pode continuar saber mais sobre a alteração do feed nos seguintes artigos:

* [Descrição geral do feed de alterações](change-feed.md)
* [Feed de alterações de formas para ler](read-change-feed.md)
* [Usando a alteração de biblioteca processador do feed](change-feed-processor.md)
* [Como trabalhar com a alteração de biblioteca processador do feed](change-feed-processor.md)
* [Computação de base de dados sem servidor com o Azure Cosmos DB e as funções do Azure](serverless-computing-database.md)
