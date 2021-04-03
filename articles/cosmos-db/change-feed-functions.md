---
title: Como usar o feed DB da Azure Cosmos com funções Azure
description: Utilize funções Azure para ligar ao feed de alteração DB do Azure Cosmos. Mais tarde, pode criar funções reativas do Azure que são desencadeadas em cada novo evento.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7f4903cf29f15132db91e47d78efe5a556efd937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340248"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquiteturas baseadas em eventos sem servidor com funções Azure Cosmos DB e Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As Funções Azure fornecem a forma mais simples de se ligar ao [feed de alteração](change-feed.md). Pode criar pequenas Funções Azure reativas que serão automaticamente ativadas em cada novo evento no feed de mudança do seu contentor Azure Cosmos.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="Funções baseadas em eventos sem servidor que funcionam com o gatilho de Funções Azure para Cosmos DB" border="false":::

Com o [gatilho de Funções Azure para Cosmos DB,](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)pode aproveitar a funcionalidade de deteção de eventos do [Processador Change Feed](./change-feed-processor.md)e de deteção de eventos fiável sem a necessidade de manter qualquer infraestrutura de [trabalho](./change-feed-processor.md). Concentre-se apenas na lógica da sua Azure Function sem se preocupar com o resto do pipeline de fornecimento de eventos. Pode até misturar o Gatilho com quaisquer [outras ligações Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Atualmente, o gatilho Azure Functions para Cosmos DB é suportado apenas para utilização com a API core (SQL).

## <a name="requirements"></a>Requisitos

Para implementar um fluxo baseado em eventos sem servidor, você precisa:

* **O recipiente monitorizado**: O recipiente monitorizado é o recipiente Azure Cosmos que está a ser monitorizado e armazena os dados a partir dos quais o feed de alteração é gerado. Quaisquer inserções, atualizações ao recipiente monitorizado refletem-se na alteração do revestimento do recipiente.
* **O recipiente de locação**: O recipiente de locação mantém o estado em várias instâncias dinâmicas de função Azure sem servidor e permite um dimensionamento dinâmico. Este recipiente de locação pode ser criado manualmente ou automaticamente pelo gatilho Azure Functions para Cosmos DB. Para criar automaticamente o recipiente de locação, deite a bandeira *CreateLeaseCollectionIfNotExists* na [configuração](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Os recipientes de locação divididos são obrigados a ter uma `/id` definição chave de partição.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Crie o seu gatilho de Funções Azure para Cosmos DB

A criação da sua Função Azure com um gatilho de Funções Azure para Cosmos DB é agora suportada em todas as integrações Azure Functions IDE e CLI:

* [Extensão do estúdio visual](../azure-functions/functions-develop-vs.md) para utilizadores do Estúdio Visual.
* [Extensão visual do código do estúdio](/azure/developer/javascript/tutorial-vscode-serverless-node-01) para utilizadores do Código do Estúdio Visual.
* E, [finalmente, core CLI ferramenta](../azure-functions/functions-run-local.md#create-func) para uma experiência agnóstica IDE de plataforma cruzada.

## <a name="run-your-trigger-locally"></a>Executar o seu gatilho localmente

Pode executar a sua [Função Azure localmente](../azure-functions/functions-develop-local.md) com o [Emulador Azure Cosmos DB](./local-emulator.md) para criar e desenvolver os seus fluxos baseados em eventos sem servidor sem uma Subscrição Azure ou incorrer em quaisquer custos.

Se quiser testar cenários ao vivo na nuvem, pode experimentar o [Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem qualquer cartão de crédito ou subscrição Azure necessária.

## <a name="next-steps"></a>Passos seguintes

Pode agora continuar a aprender mais sobre a alteração do feed nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Formas de ler change feed](read-change-feed.md)
* [Utilização da biblioteca de processadores de feed de alteração](change-feed-processor.md)
* [Como trabalhar com a biblioteca de processadores change feed](change-feed-processor.md)
* [Computação de base de dados sem servidor usando funções Azure Cosmos DB e Azure](serverless-computing-database.md)
