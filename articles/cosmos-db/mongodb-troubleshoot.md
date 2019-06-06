---
title: Resolver erros comuns na API do Azure Cosmos DB para Mongo DB
description: Este documento discute as opções para solucionar problemas comuns encontrados na API do Azure Cosmos DB para o MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735710"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Resolver problemas comuns na API do Azure Cosmos DB para o MongoDB

O Azure Cosmos DB implementa os protocolos de transmissão de comuns bases de dados NoSQL, incluindo o MongoDB. Devido à implementação de protocolo durante a transmissão, forma transparente pode interagir com o Azure Cosmos DB ao utilizar os SDKs de cliente existente, drivers e ferramentas que funcionam com bases de dados NoSQL. O Azure Cosmos DB não utilizar nenhum código-fonte das bases de dados para fornecer APIs compatíveis com o durante a transmissão para qualquer um dos bancos de dados NoSQL. Qualquer controlador de cliente do MongoDB que compreende as versões de protocolo de rede pode ligar ao Azure Cosmos DB.

API do Azure Cosmos DB para o MongoDB é compatível com a versão 3.2 do protocolo do MongoDB (os operadores de consulta e os recursos adicionados na versão 3.4 estão atualmente disponíveis como pré-visualização), mas há alguns códigos de erro personalizada que correspondem ao Azure Cosmos DB erros específicos. Este artigo explica os passos para resolver esses erros, códigos de erro e erros diferentes.

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de pedido consumida é mais do que a taxa de unidade de pedido de aprovisionamento para a coleção e otimizado. | Considere dimensionar o débito atribuído a um contentor ou um conjunto de contentores a partir do portal do Azure ou pode repetir a operação. |
| ExceededMemoryLimit | 16501 | Como um serviço de multi-inquilino, tornou-se a operação ao longo de alocação de memória do cliente. | Reduza o âmbito da operação por meio de critérios de consulta mais restritivas ou contacte o suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Problemas de versão de durante a transmissão do MongoDB | - | As versões mais antigas de drivers de MongoDB são não é possível detetar o nome da conta do Azure Cosmos nas cadeias de ligação. | Acrescentar *appName = @**accountName** @*  no final da API do Cosmos DB para a cadeia de ligação do MongoDB, em que ***accountName*** é o nome da sua conta do Cosmos DB . |


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para o MongoDB.
- Saiba como [utilizar Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para o MongoDB.
- Explorar o MongoDB [amostras](mongodb-samples.md) com a API do Azure Cosmos DB para o MongoDB.

