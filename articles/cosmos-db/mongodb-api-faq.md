---
title: Perguntas frequentes sobre a API do Azure Cosmos DB para mongoDB
description: Obtenha respostas a perguntas frequentes sobre a API do Azure Cosmos DB para o MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: de75ea1bc0a1cf63c74be3f7d9e486e1fe38db6f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614567"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Perguntas frequentes sobre a API do Azure Cosmos DB para mongoDB

A API do Azure Cosmos DB para MongoDB é uma camada de compatibilidade de protocolo sinuoso que permite às aplicações comunicar de forma fácil e transparente com o motor de base de dados nativo Azure Cosmos utilizando SDKs e condutores existentes e apoiados pela comunidade para o MongoDB. Os desenvolvedores podem agora usar as cadeias de ferramentas e competências mongoDB existentes para construir aplicações que tirem partido do Azure Cosmos DB. Os desenvolvedores beneficiam das capacidades únicas do Azure Cosmos DB, que incluem distribuição global com replicação multi-master, indexação automática, manutenção de backup, acordos de nível de serviço apoiados financeiramente (SLAs) etc.

## <a name="how-do-i-connect-to-my-database"></a>Como me ligo à minha base de dados?

A forma mais rápida de se ligar a uma base de dados da Cosmos com a API da Azure Cosmos DB para o MongoDB é dirigir-se ao [portal Azure.](https://portal.azure.com) Vá à sua conta e, em seguida, no menu de navegação à esquerda, clique em **Quick Start**. O Quickstart é a melhor forma de obter códigos para ligar à sua base de dados.

A Azure Cosmos DB impõe requisitos e padrões de segurança rigorosos. As contas Azure Cosmos DB requerem autenticação e comunicação segura via TLS, por isso certifique-se de usar TLSv1.2.

Para mais informações, consulte [Connect to your Cosmos database with Azure Cosmos DB's API for MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Códigos de erro ao utilizar a API da Azure Cosmos DB para MongoDB?

Juntamente com os códigos de erro comuns do MongoDB, a API do Azure Cosmos DB para a MongoDB tem os seus próprios códigos de erro específicos:

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de pedido consumidas é superior à taxa de unidade de pedido prevista para o contentor e foi estrangulada. | Considere escalpelizar a entrada atribuída a um recipiente ou a um conjunto de recipientes do portal Azure ou a tentar novamente. |
| LimiteMemóriaExcedido | 16501 | Como serviço multi-inquilinos, a operação passou por cima do loteamento de memória do cliente. | Reduzir o âmbito da operação através de critérios de consulta mais restritivos ou suporte de contacto do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br> Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>Motoristas apoiados

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>O condutor da Simba para a MongoDB é suportado para uso com a API da Azure Cosmos DB para o MongoDB?

Sim, você pode usar o motorista Mongo ODBC de Simba com API da Azure Cosmos DB para MongoDB

## <a name="next-steps"></a>Passos seguintes

* [Construa uma aplicação web .NET utilizando a API do Azure Cosmos DB para o MongoDB](create-mongodb-dotnet.md)
* [Crie uma aplicação de consola com java e a API MongoDB em Azure Cosmos DB](create-mongodb-java.md)
