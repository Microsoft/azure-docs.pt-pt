---
title: Como utilizar o feed de mudança de DB Azure Cosmos com funções azure
description: Utilize funções Azure para ligar ao feed de mudança de DB Azure Cosmos. Mais tarde poderá criar funções de Azure reativas que são desencadeadas em todos os novos eventos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851365"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquiteturas baseadas em eventos sem servidores com funções Azure Cosmos DB e Azure

As Funções Azure fornecem a forma mais simples de ligar ao feed de [mudança](change-feed.md). Pode criar pequenas funções azure reativas que serão automaticamente desencadeadas em cada novo evento no feed de mudança do seu contentor Azure Cosmos.

![Funções baseadas em eventos sem servidor estoque trabalhando com o gatilho funções Azure para Cosmos DB](./media/change-feed-functions/functions.png)

Com o [gatilho das Funções Azure para cosmos DB,](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)pode aproveitar a funcionalidade de deteção de eventos de escala e de deteção de eventos fiável do [Processador Change Feed](./change-feed-processor.md)sem a necessidade de manter qualquer infraestrutura de [trabalhadores.](./change-feed-processor.md) Concentre-se na lógica da sua Função Azure sem se preocupar com o resto do oleoduto de fornecimento de eventos. Pode até misturar o Gatilho com quaisquer [outras ligações de Funções Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Atualmente, o gatilho funções Azure para Cosmos DB é suportado para utilização apenas com a API Core (SQL).

## <a name="requirements"></a>Requisitos

Para implementar um fluxo baseado em eventos sem servidor, precisa de:

* **O recipiente monitorizado**: O contentor monitorizado é o contentor Azure Cosmos a ser monitorizado e armazena os dados a partir dos quais o feed de mudança é gerado. Quaisquer inserções, as atualizações do recipiente monitorizado refletem-se na alimentação de alterações do recipiente.
* **O contentor de aluguer**: O contentor de aluguer mantém o estado em várias e dinâmicas instâncias de função Azure sem servidor e permite uma escala dinâmica. Este recipiente de aluguer pode ser criado manualmente ou automaticamente pelo gatilho funções Azure para Cosmos DB. Para criar automaticamente o recipiente de aluguer, detete a bandeira *CreateLeaseCollectionIfNotExist* na [configuração](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Os recipientes de locação divisória são obrigados a ter uma definição chave de divisória `/id`.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Crie o gatilho de Funções Azure para Cosmos DB

A criação da sua Função Azure com um gatilho de funções Azure para cosmos DB é agora suportada em todas as integrações Azure FunctionI e CLI:

* [Extensão do estúdio visual](../azure-functions/functions-develop-vs.md) para utilizadores do Estúdio Visual.
* [Extensão](/azure/javascript/tutorial-vscode-serverless-node-01) visual do código do estúdio para utilizadores de Código de Estúdio Visual.
* E finalmente [core CLI ferramentas](../azure-functions/functions-run-local.md#create-func) para uma experiência agnóstica ide de plataforma cruzada.

## <a name="run-your-trigger-locally"></a>Execute o seu gatilho localmente

Pode executar a sua [Função Azure localmente](../azure-functions/functions-develop-local.md) com o [Emulador Db Azure Cosmos](./local-emulator.md) para criar e desenvolver os seus fluxos baseados em eventos sem servidores sem uma Subscrição Azure ou incorrer em quaisquer custos.

Se quiser testar cenários ao vivo na nuvem, pode [experimentar o Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem qualquer cartão de crédito ou subscrição Azure necessária.

## <a name="next-steps"></a>Passos seguintes

Pode agora continuar a aprender mais sobre a mudança de alimentos nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Formas de ler o feed de mudança](read-change-feed.md)
* [Usando a biblioteca de processadores de feed de mudança](change-feed-processor.md)
* [Como trabalhar com a biblioteca de processadores de feed de mudança](change-feed-processor.md)
* [Computação de base de dados sem servidores utilizando funções Azure Cosmos DB e Azure](serverless-computing-database.md)
