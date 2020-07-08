---
title: Resolução de problemas erros comuns na API da Azure Cosmos DB para a Mongo DB
description: Este doc discute as formas de resolver problemas comuns encontrados na API da Azure Cosmos DB para o MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75941836"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Resolução de problemas comuns na API da Azure Cosmos DB para a MongoDB

A Azure Cosmos DB implementa os protocolos de fio de bases de dados noSQL comuns, incluindo o MongoDB. Devido à implementação do protocolo de fio, pode interagir de forma transparente com a Azure Cosmos DB utilizando os SDKs, controladores e ferramentas existentes que funcionam com bases de dados NoSQL. A Azure Cosmos DB não utiliza nenhum código-fonte das bases de dados para fornecer APIs compatíveis com fios para qualquer uma das bases de dados NoSQL. Qualquer controlador cliente da MongoDB que compreenda as versões do protocolo de fio pode ligar-se à Azure Cosmos DB.

Embora a API da Azure Cosmos DB para o MongoDB seja compatível com a versão 3.2 do protocolo de fio do MongoDB (os operadores de consulta e funcionalidades adicionadas na versão 3.4 estão atualmente disponíveis como pré-visualização), existem alguns códigos de erro personalizados que correspondem a erros específicos do Azure Cosmos DB. Este artigo explica diferentes erros, códigos de erro e os passos para resolver esses erros.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de pedido consumidas é superior à taxa de unidade de pedido prevista para a recolha e foi estrangulado. | Considere escalonar a produção atribuída a um contentor ou a um conjunto de contentores do portal Azure ou pode voltar a tentar a operação. |
| LimiteMemóriaExcedido | 16501 | Como serviço multi-inquilino, a operação passou por cima do loteamento de memória do cliente. | Reduzir o âmbito de funcionamento através de critérios de consulta mais restritivos ou suporte de contacto do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A trajetória de índice correspondente ao item de encomenda especificado está excluída / A ordem por consulta não tem um índice composto correspondente do qual possa ser servido. | 2 | A consulta solicita uma espécie num campo que não está indexado. | Crie um índice de correspondência (ou índice composto) para a consulta de tipo que está a ser tentada. |
| Problemas da versão wire do MongoDB | - | As versões mais antigas dos condutores do MongoDB são incapazes de detetar o nome da conta Azure Cosmos nas cordas de ligação. | Append *appName=@**contaName** @ * no final da sua API de ligação cosmos DB para a cadeia de conexão MongoDB, onde o nome de ***contaName*** é o seu nome de conta Cosmos DB. |


## <a name="next-steps"></a>Próximos passos

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.

