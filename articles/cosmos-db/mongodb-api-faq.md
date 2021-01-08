---
title: Perguntas frequentes sobre a API da Azure Cosmos DB para a MongoDB
description: Obtenha respostas a perguntas frequentes sobre a API da Azure Cosmos para a MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019023"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Perguntas frequentes sobre a API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A API da Azure Cosmos DB para a MongoDB é uma camada de compatibilidade de protocolos de fio que permite que as aplicações se comuniquem de forma fácil e transparente com o motor de base de dados Azure Cosmos nativo, utilizando SDKs e condutores existentes e suportados pela comunidade para o MongoDB. Os desenvolvedores podem agora usar as ferramentas e habilidades mongoDB existentes para construir aplicações que tirem partido do Azure Cosmos DB. Os desenvolvedores beneficiam das capacidades únicas da Azure Cosmos DB, que incluem distribuição global com replicação de escrita multi-região, auto-indexação, manutenção de backup, acordos de nível de serviço apoiados financeiramente (SLAs) etc.

## <a name="how-do-i-connect-to-my-database"></a>Como posso ligar-me à minha base de dados?

A forma mais rápida de ligar a uma base de dados cosmos com a API da Azure Cosmos para a MongoDB é dirigir-se ao [portal Azure.](https://portal.azure.com) Vá à sua conta e, em seguida, no menu de navegação à esquerda, clique em **Quick Start**. O Quickstart é a melhor forma de obter códigos para ligar à sua base de dados.

A Azure Cosmos DB aplica rigorosos requisitos e padrões de segurança. As contas DB da Azure Cosmos requerem autenticação e comunicação segura via TLS, por isso não se esqueça de utilizar o TLSv1.2.

Para mais informações, consulte [a sua base de dados Cosmos com a API da Azure Cosmos DB para a MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Códigos de erro durante a utilização da API da Azure Cosmos DB para o MongoDB?

Juntamente com os códigos de erro comuns do MongoDB, a API da Azure Cosmos DB para a MongoDB tem os seus próprios códigos de erro específicos. Estes podem ser encontrados no [Guia de Resolução de Problemas.](mongodb-troubleshoot.md)

## <a name="supported-drivers"></a>Motoristas apoiados

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>O condutor do Simba para a MongoDB é apoiado para uso com a API da Azure Cosmos DB para a MongoDB?

Sim, você pode usar o motorista Mongo ODBC de Simba com Azure Cosmos DB's API para MongoDB

## <a name="next-steps"></a>Próximos passos

* [Construa uma aplicação web .NET utilizando a API da Azure Cosmos DB para a MongoDB](create-mongodb-dotnet.md)
* [Crie uma app de consolas com a Java e a MongoDB API em Azure Cosmos DB](create-mongodb-java.md)
