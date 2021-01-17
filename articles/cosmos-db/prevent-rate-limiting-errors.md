---
title: Evitar erros de limitação de taxas para Azure Cosmos DB API para operações mongoDB.
description: Saiba como evitar que o seu API DB AZure Cosmos para operações mongoDB acerte erros de limitação de taxa com a função SSR (retrip lateral do servidor).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540734"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Evitar erros de limitação de taxas para a Azure Cosmos DB API para operações da MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A Azure Cosmos DB API para operações mongoDB pode falhar com erros limitadores de taxa (16500/429) se excederem o limite de produção de uma coleção (RUs). 

Pode ativar a função 'Retripar' (SSR) do lado do servidor e deixar o servidor voltar a tentar estas operações automaticamente. Os pedidos são novamente julgados após um curto atraso para todas as cobranças na sua conta. Esta funcionalidade é uma alternativa conveniente para lidar com erros de limitação de tarifas na aplicação do cliente.


## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para a sua Azure Cosmos DB API para a conta MongoDB.

1. Vá ao painel **de funcionalidades** por baixo da secção **Definições.**

1. Selecione **a retry do lado do servidor**.

1. Clique **em Ativar** para ativar esta funcionalidade para todas as coleções da sua conta.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Screenshot da funcionalidade de relagem lateral do servidor para Azure Cosmos DB API para MongoDB":::


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a resolução de erros comuns, consulte este artigo:

* [Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB](mongodb-troubleshoot.md)
