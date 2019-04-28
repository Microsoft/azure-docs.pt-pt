---
title: Introdução à API do Azure Cosmos DB para MongoDB
description: Saiba como pode utilizar o Azure Cosmos DB para armazenar e consultar grandes quantidades de dados com a API do Azure Cosmos DB do MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: d0f61afaba094a1e499a91f9937a31554438759e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331538"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O [Azure Cosmos DB](introduction.md) é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft para aplicações críticas para atividades. O Azure Cosmos DB proporciona [distribuição global chave na mão](distribute-data-globally.md), [dimensionamento elástico de débito e de armazenamento](partition-data.md) em todo o mundo, latências de milissegundos de um só dígito no percentil 99 e elevada disponibilidade garantida, tudo com o suporte de [SLA líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa automaticamente os dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem que tenha de lidar com a gestão de esquemas e índices. É multimodal e suporte modelos de dados em documentos, chaves-valores, gráficos e em colunas. Por predefinição, pode interagir com o Cosmos DB com a API de SQL. Além disso, o serviço Cosmos DB implementa os protocolos de transmissão para APIs de NoSQL comuns, incluindo, Cassandra, MongoDB, Gremlin e armazenamento de tabelas do Azure. Isto permite-lhe utilizar o seu controlador de cliente familiar do NoSQL e as ferramentas para interagir com a base de dados do Cosmos.

## <a name="wire-protocol-compatibility"></a>Compatibilidade de protocolo de transmissão

O Azure Cosmos DB implementa os protocolos de transmissão de bases de dados do NoSQL comuns incluindo, Cassandra, MongoDB, Gremlin e armazenamento de tabelas do Azure. Ao fornecer uma implementação nativa os protocolos de transmissão diretamente e eficiente dentro do Cosmos DB, ele permite que existente SDKs do cliente, drivers e ferramentas das bases de dados NoSQL interagir com o Cosmos DB de forma transparente. O cosmos DB não utiliza nenhum código-fonte das bases de dados para fornecer APIs compatíveis com o durante a transmissão para qualquer um dos bancos de dados NoSQL.

Por predefinição, a API do Azure Cosmos DB para o MongoDB é compatível com a versão 3.2 do protocolo de transmissão do MongoDB. Recursos ou adicionados na versão 3.4 do protocolo de transmissão de operadores de consulta estão atualmente disponíveis como uma funcionalidade de pré-visualização. Qualquer controlador de cliente do MongoDB que entende essas versões de protocolo deve ser capaz de forma nativa a ligar ao Cosmos DB.

![API do Azure Cosmos DB para MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

## <a name="key-benefits"></a>Principais vantagens

Os principais benefícios do Cosmos DB como uma base de dados totalmente gerido, globalmente distribuído, como um serviço são descritos [aqui](introduction.md). Além disso, ao implementar nativamente protocolos de transmissão de APIs populares de NoSQL, o Cosmos DB fornece as seguintes vantagens:

* Migre facilmente a sua aplicação para o Cosmos DB, preservando a partes significativas da lógica do aplicativo.
* Mantenha seu aplicativo portátil e continuará desconhecidas do fornecedor na cloud.
* Obtenha setor SLAs líderes do setor, com apoio financeiro para as APIs do NoSQL comuns com tecnologia do Cosmos DB.
* Dimensionar de forma elástica o débito aprovisionado e armazenamento para as bases de dados do Cosmos com base nas suas necessidades e pague apenas o débito e armazenamento de que precisa. Isso leva à redução significativa de custos.
* Distribuição de chave na mão, global com a replicação de vários mestre.

## <a name="cosmos-dbs-api-for-mongodb"></a>API do cosmos DB para o MongoDB

Siga os guias de introdução para criar uma conta do Cosmos e migrar a sua aplicação MongoDB existente para utilizar o Azure Cosmos DB ou criar um novo:

* [Migrar uma aplicação de web de node. js do MongoDB existente](create-mongodb-nodejs.md).
* [Criar uma aplicação web com a API do Azure Cosmos DB para MongoDB e o SDK de .NET](create-mongodb-dotnet.md)
* [Criar uma aplicação de consola com a API do Azure Cosmos DB para MongoDB e o SDK de Java](create-mongodb-java.md)

## <a name="next-steps"></a>Passos Seguintes

Eis alguns sítios por onde começar:

* Siga os [ligar uma aplicação MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) tutorial para saber como obter as informações de cadeia de ligação de conta.
* Siga os [utilize o Studio 3T com o Azure Cosmos DB](mongodb-mongochef.md) tutorial para saber como criar uma ligação entre a base de dados do Cosmos e uma aplicação MongoDB no Studio t 3.
* Siga os [MongoDB importar dados para o Azure Cosmos DB](mongodb-migrate.md) tutorial para importar os dados para uma base de dados do Cosmos.
* Ligar a uma conta do Cosmos com [Robo 3T](mongodb-robomongo.md).
* Saiba como [configurar ler preferências para aplicações distribuídas mundialmente](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Nota: Este artigo descreve um recurso do Azure Cosmos DB, que fornece a compatibilidade de protocolo de transmissão com bancos de dados do MongoDB. Microsoft não é executado para fornecer este serviço de bases de dados MongoDB. O Azure Cosmos DB não está afiliado a MongoDB, Inc.</sup>
