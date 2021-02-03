---
title: Evitar erros de limitação de taxas para Azure Cosmos DB API para operações mongoDB.
description: Saiba como evitar que o seu API DB AZure Cosmos para operações mongoDB acerte erros de limitação de taxa com a função SSR (retrip lateral do servidor).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507935"
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

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

1. Verifique se a SSR já está ativada para a sua conta:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Ativar** SSR para todas as coleções na sua conta de base de dados. Pode levar até 15 min para que esta mudança entre em vigor.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
O seguinte comando irá **desativar a** SSR para todas as coleções na sua conta de base de dados, removendo "DisableRateLimitingResponses" da lista de capacidades. Pode levar até 15 min para que esta mudança entre em vigor.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes
* Como são re-julgados os pedidos?
    * Os pedidos são re-experimentados continuamente (uma e outra vez) até que um intervalo de 60 segundos seja atingido. Se o tempo limite for atingido, o cliente receberá uma [exceção ExceedTimeLimit (50)](mongodb-troubleshoot.md).
*  Como posso monitorizar os efeitos da SSR?
    *  Pode ver os erros de limitação de taxa (429s) que são novamente experimentados no painel de métricas do Cosmos DB. Tenha em mente que estes erros não vão para o cliente quando a SSR está ativada, uma vez que são manuseadas e novamente experimentadas do lado do servidor. 
    *  Pode pesquisar por entradas de registo que contenham "EstimatedDelayFromRateLimitingInMilliseconds" nos [seus registos de recursos Do Cosmos DB](cosmosdb-monitor-resource-logs.md).
*  A SSR vai afetar o meu nível de consistência?
    *  A SSR não afeta a consistência do pedido. Os pedidos são novamente experimentados do lado do servidor se forem limitados (com um erro de 429). 
*  A SSR afeta qualquer tipo de erro que o meu cliente possa receber?
    *  Não, a SSR apenas afeta os erros de limitação de taxas (429s) redando-os do lado do servidor. Esta funcionalidade impede-o de ter de lidar com erros de limitação de tarifas na aplicação do cliente. Todos os [outros erros](mongodb-troubleshoot.md) irão para o cliente. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a resolução de erros comuns, consulte este artigo:

* [Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB](mongodb-troubleshoot.md)
