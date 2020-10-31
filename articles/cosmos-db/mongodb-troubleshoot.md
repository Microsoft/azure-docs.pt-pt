---
title: Resolução de problemas erros comuns na API da Azure Cosmos DB para a Mongo DB
description: Este doc discute as formas de resolver problemas comuns encontrados na API da Azure Cosmos DB para o MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: fa33e2ccc5c6cca94ab4e2294a4865745145c1ce
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096330"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O seguinte artigo descreve erros e soluções comuns para bases de dados utilizando a API API AZure Cosmos DB para a MongoDB.

>[!Note]
> A Azure Cosmos DB não acolhe o motor MongoDB. Fornece uma implementação da versão [3.6](mongodb-feature-support-36.md) do protocolo de arame MongoDB e suporte legado para [a versão 3.2](mongodb-feature-support.md)do protocolo de fio, portanto, alguns destes erros só são encontrados na API da Azure Cosmos DB para a MongoDB. 

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

| Erro               | Código  | Description  | Solução  |
|---------------------|-------|--------------|-----------|
| UltrapassadoTimeLimit   | 50 | O pedido excedeu o tempo limite de 60 segundos de execução. | Pode haver muitas causas para este erro. Uma das causas é quando a capacidade atual das unidades de pedido alocadas não é suficiente para concluir o pedido. Isto pode ser resolvido ao aumentar as unidades de pedido dessa coleção ou base de dados. Noutros casos, este erro pode ser trabalhado ao dividir um grande pedido em outros mais pequenos. |
| TooManyRequests     | 16500 | O número total de unidades de pedido consumidas é maior do que a taxa pedido/unidade aprovisionada para a coleção e foi limitada. | Pondere dimensionar o débito atribuído a um contentor ou a um conjunto de contentores do portal do Azure ou pode repetir a operação. |
| LimiteMemóriaExcedido | 16501 | Como serviço multi-inquilino, a operação passou por cima do loteamento de memória do cliente. | Reduzir o âmbito de funcionamento através de critérios de consulta mais restritivos ou suporte de contacto do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| O caminho de índice correspondente à ordem especificada por item foi excluído/A ordem por consulta não tem um índice composto correspondente a partir do qual possa ser servida. | 2 | A consulta pede uma ordenação num campo que não está indexado. | Crie um índice de correspondência (ou índice composto) para a consulta de tipo que está a ser tentada. |
| Problemas da versão de transmissão do MongoDB | - | As versões mais antigas dos condutores do MongoDB são incapazes de detetar o nome da conta Azure Cosmos nas cordas de ligação. | Append *appName=@ **contaName** @* no final da sua API de ligação cosmos DB para a cadeia de conexão MongoDB, onde o nome de ***contaName*** é o seu nome de conta Cosmos DB. |

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.

