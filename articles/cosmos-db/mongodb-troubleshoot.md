---
title: Solucionar erros comuns na API do Azure Cosmos DB para o Mongo DB
description: Este documento discute as maneiras de solucionar problemas comuns encontrados na API do Azure Cosmos DB para MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: ece975fa37e500b1c160210684a0cb46e719c48b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754959"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Solucionar problemas comuns na API do Azure Cosmos DB para MongoDB

Azure Cosmos DB implementa os protocolos de conexão de bancos de dados NoSQL comuns, incluindo o MongoDB. Devido à implementação do protocolo de conexão, você pode interagir de forma transparente com Azure Cosmos DB usando os SDKs, os drivers e as ferramentas do cliente existentes que funcionam com bancos de dados NoSQL. Azure Cosmos DB não usa nenhum código-fonte dos bancos de dados para fornecer APIs compatíveis com cabo para qualquer um dos bancos de dados NoSQL. Qualquer driver de cliente do MongoDB que entenda as versões de protocolo de conexão pode se conectar ao Azure Cosmos DB.

Embora a API do Azure Cosmos DB para MongoDB seja compatível com a versão 3,2 do protocolo de conexão do MongoDB (os operadores de consulta e os recursos adicionados na versão 3,4 estão atualmente disponíveis como uma visualização), há alguns códigos de erro personalizados que correspondem a Azure Cosmos DB erros específicos. Este artigo explica erros diferentes, códigos de erro e as etapas para resolver esses erros.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas é maior que a taxa de unidade de solicitação provisionada para a coleção e foi limitada. | Considere dimensionar a taxa de transferência atribuída a um contêiner ou a um conjunto de contêineres do portal do Azure ou você pode repetir a operação. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação passou pela alocação de memória do cliente. | Reduza o escopo da operação por meio de critérios de consulta mais restritivos ou entre em contato com o suporte da [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| O caminho de índice correspondente ao item de ordem por especificado é excluído/a consulta order by não tem um índice composto correspondente do qual ele pode ser atendido. | 2 | A consulta solicita uma classificação em um campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de classificação que está sendo tentada. |
| Problemas de versão de conexão do MongoDB | - | As versões mais antigas dos drivers do MongoDB não conseguem detectar o nome da conta do Azure Cosmos nas cadeias de conexão. | Acrescente *AppName = @**accountName**@* ao final da cadeia de conexão da API do cosmos DB para MongoDB, em que ***accountName*** é o nome da sua conta de Cosmos DB. |


## <a name="next-steps"></a>Passos seguintes

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore os [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.

