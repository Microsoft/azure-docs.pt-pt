---
title: Problemas de resolução de problemas na API da Azure Cosmos DB para Mongo DB
description: Este doc discute as formas de resolver problemas comuns encontrados na API da Azure Cosmos DB para o MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75941836"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Problemas de resolução de problemas na API da Azure Cosmos DB para o MongoDB

A Azure Cosmos DB implementa os protocolos de arame de bases de dados noSQL comuns, incluindo o MongoDB. Devido à implementação do protocolo de arame, pode interagir de forma transparente com a Azure Cosmos DB utilizando os SDKs, motoristas e ferramentas existentes que funcionam com bases de dados NoSQL. O Azure Cosmos DB não utiliza nenhum código fonte das bases de dados para fornecer APIs compatíveis com fios para qualquer uma das bases de dados noSQL. Qualquer condutor de cliente da MongoDB que compreenda as versões de protocolo de arame pode ligar-se ao Azure Cosmos DB.

Embora a API da Azure Cosmos DB para mongoDB seja compatível com a versão 3.2 do protocolo de arame do MongoDB (os operadores de consulta e funcionalidades adicionadas na versão 3.4 estão atualmente disponíveis como pré-visualização), existem alguns códigos de erro personalizados que correspondem a erros específicos do Azure Cosmos DB. Este artigo explica diferentes erros, códigos de erro e os passos para resolver esses erros.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de pedido consumidas é superior à taxa de unidade de pedido prevista para a recolha e foi estrangulada. | Considere escalonar a entrada atribuída a um recipiente ou a um conjunto de contentores do portal Azure ou pode voltar a tentar a operação. |
| LimiteMemóriaExcedido | 16501 | Como serviço multi-inquilinos, a operação passou por cima do loteamento de memória do cliente. | Reduzir o âmbito da operação através de critérios de consulta mais restritivos ou suporte de contacto do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A trajetória do índice correspondente ao item de encomenda especificado está excluída / A ordem por consulta não tem um índice composto correspondente do que pode ser servido. | 2 | A consulta solicita uma espécie num campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de tipo que está a ser tentada. |
| Problemas da versão wire do MongoDB | - | As versões mais antigas dos condutores do MongoDB não conseguem detetar o nome da conta Azure Cosmos nas cordas de ligação. | Append *appName=@**accountName** @ * no final da API do Cosmos DB para a cadeia de conexão MongoDB, onde ***o nome*** da conta é o seu nome de conta Cosmos DB. |


## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para MongoDB.
- Aprenda a [usar robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para MongoDB.
- Explore [as amostras](mongodb-samples.md) de MongoDB com a API da Azure Cosmos DB para o MongoDB.

