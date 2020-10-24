---
title: Introdução à API da Azure Cosmos DB para a MongoDB
description: Saiba como pode usar a Azure Cosmos DB para armazenar e consultar quantidades massivas de dados usando a API da Azure Cosmos DB para a MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c3101b69b7ebf55367dbb52d0c2fd6d265cf33f5
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475639"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O [Azure Cosmos DB](introduction.md) é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft para aplicações críticas para atividades. O Azure Cosmos DB proporciona [distribuição global chave na mão](distribute-data-globally.md), [dimensionamento elástico de débito e de armazenamento](partitioning-overview.md) em todo o mundo, latências de milissegundos de um só dígito no percentil 99 e elevada disponibilidade garantida, tudo com o suporte de [SLA líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa automaticamente os dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem que tenha de lidar com a gestão de esquemas e índices. É multimodal e suporte modelos de dados em documentos, chaves-valores, gráficos e em colunas. O serviço DB da Azure Cosmos implementa protocolos de fios para APIs noSQL comuns, incluindo Cassandra, MongoDB, Gremlin e Azure Table Storage. Isto permite-lhe utilizar os seus familiares controladores e ferramentas de clientes NoSQL para interagir com a sua base de dados Cosmos.

## <a name="wire-protocol-compatibility"></a>Compatibilidade do protocolo de arame

A Azure Cosmos DB implementa o protocolo de fio para o MongoDB. Esta implementação permite uma compatibilidade transparente com SDKs, motoristas e ferramentas de clientes nativos da MongoDB. A Azure Cosmos DB acolhe o motor de base de dados MongoDB. Os detalhes das funcionalidades suportadas pela MongoDB podem ser consultados aqui: 
- [A API da Azure Cosmos DB para a versão 3.6 do motor mongo DB](mongodb-feature-support-36.md)
- [A API da Azure Cosmos DB para a versão 3.2 do motor da Mongo DB](mongodb-feature-support.md)

Por predefinição, as novas contas criadas utilizando a API da Azure Cosmos para a MongoDB são compatíveis com a versão 3.6 do protocolo de fio MongoDB. Qualquer controlador cliente da MongoDB que compreenda esta versão protocolar deve ser capaz de ligar-se de forma nativa à Cosmos DB.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="API do Azure Cosmos DB para MongoDB" border="false":::

## <a name="key-benefits"></a>Principais vantagens

Os principais benefícios da Cosmos DB como uma base de dados totalmente gerida e distribuída globalmente como um serviço são descritos [aqui.](introduction.md) Adicionalmente, ao implementar nativamente protocolos de fios de APIs populares no NoSQL, a Cosmos DB fornece os seguintes benefícios:

* Migrar facilmente a sua aplicação para Cosmos DB preservando partes significativas da sua lógica de aplicação.
* Mantenha a sua aplicação portátil e continue a permanecer em nuvem agnóstica.
* Obtenha sLAs líderes da indústria, apoiados financeiramente para as APIs noSQL comuns alimentadas pela Cosmos DB.
* Dimensione elasticamente a produção e armazenamento previstos para as suas bases de dados Cosmos com base na sua necessidade e pague apenas pela produção e armazenamento de que necessita. Isto leva a uma poupança significativa de custos.
* Chave na mão, distribuição global com multi-região escreve replicação.

## <a name="cosmos-dbs-api-for-mongodb"></a>API da Cosmos DB para o MongoDB

Siga os quickstarts para criar uma conta Azure Cosmos e migrar a sua aplicação MongoDB existente para usar a Azure Cosmos DB, ou construir uma nova:

* [Migrar uma aplicação web Node.js MongoDB existente.](create-mongodb-nodejs.md)
* [Construa uma aplicação web utilizando a API da Azure Cosmos DB para a MongoDB e .NET SDK](create-mongodb-dotnet.md)
* [Construa uma app de consolas utilizando a API da Azure Cosmos DB para a MongoDB e a Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Passos seguintes

Eis alguns sítios por onde começar:

* Siga a [aplicação Connect a MongoDB para o tutorial DB do Azure Cosmos](connect-mongodb-account.md) para aprender a obter informações sobre as cordas de ligação da sua conta.
* Siga o tutorial [use Studio 3T com Azure Cosmos DB](mongodb-mongochef.md) para aprender a criar uma ligação entre a sua base de dados Cosmos e a aplicação MongoDB no Studio 3T.
* Siga os [dados do Import MongoDB no tutorial do Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json) para importar os seus dados para uma base de dados cosmos.
* Ligue-se a uma conta Cosmos usando [Robo 3T](mongodb-robomongo.md).
* Saiba como [configurar as preferências de leitura para aplicações distribuídas globalmente.](../cosmos-db/tutorial-global-distribution-mongodb.md)
* Encontre as soluções para erros geralmente encontrados no nosso [guia de resolução de problemas](mongodb-troubleshoot.md)


<sup>Nota: Este artigo descreve uma característica da Azure Cosmos DB que fornece compatibilidade de protocolos de fio com bases de dados MongoDB. A Microsoft não gere bases de dados mongoDB para fornecer este serviço. AZure Cosmos DB não é afiliado à MongoDB, Inc.</sup>