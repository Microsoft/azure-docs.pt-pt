---
title: Resolução de problemas erros comuns na API da Azure Cosmos DB para a Mongo DB
description: Este doc discute as formas de resolver problemas comuns encontrados na API da Azure Cosmos DB para o MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: lbosq
ms.openlocfilehash: f75374fc88923a0f131d513bebf0ffe1feeca359
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076759"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB

O seguinte artigo descreve erros e soluções comuns para bases de dados utilizando a API API AZure Cosmos DB para a MongoDB.

>[!Note]
> A Azure Cosmos DB não acolhe o motor MongoDB. Fornece uma implementação da versão [3.6](mongodb-feature-support-36.md) do protocolo de arame MongoDB e suporte legado para [a versão 3.2](mongodb-feature-support.md)do protocolo de fio, portanto, alguns destes erros só são encontrados na API da Azure Cosmos DB para a MongoDB. 

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| UltrapassadoTimeLimit   | 50 | O pedido excedeu o tempo limite de 60 segundos de execução. | Pode haver muitas causas para este erro. Uma das causas é quando a capacidade atual das unidades de pedido não é suficiente para completar o pedido. Isto pode ser resolvido aumentando as unidades de pedido dessa recolha ou base de dados. Noutros casos, este erro pode ser trabalhado ao dividir um grande pedido em outros mais pequenos. |
| TooManyRequests     | 16500 | O número total de unidades de pedido consumidas é superior à taxa de unidade de pedido prevista para a recolha e foi estrangulado. | Considere escalonar a produção atribuída a um contentor ou a um conjunto de contentores do portal Azure ou pode voltar a tentar a operação. |
| LimiteMemóriaExcedido | 16501 | Como serviço multi-inquilino, a operação passou por cima do loteamento de memória do cliente. | Reduzir o âmbito de funcionamento através de critérios de consulta mais restritivos ou suporte de contacto do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A trajetória de índice correspondente ao item de encomenda especificado está excluída / A ordem por consulta não tem um índice composto correspondente do qual possa ser servido. | 2 | A consulta solicita uma espécie num campo que não está indexado. | Crie um índice de correspondência (ou índice composto) para a consulta de tipo que está a ser tentada. |
| Problemas da versão wire do MongoDB | - | As versões mais antigas dos condutores do MongoDB são incapazes de detetar o nome da conta Azure Cosmos nas cordas de ligação. | Append *appName=@**contaName** @ * no final da sua API de ligação cosmos DB para a cadeia de conexão MongoDB, onde o nome de ***contaName*** é o seu nome de conta Cosmos DB. |

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.

