---
title: Introdução à API do Azure Cosmos DB para MongoDB
description: Saiba como você pode usar Azure Cosmos DB para armazenar e consultar grandes quantidades de dados usando a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754982"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O [Azure Cosmos DB](introduction.md) é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft para aplicações críticas para atividades. O Azure Cosmos DB proporciona [distribuição global chave na mão](distribute-data-globally.md), [dimensionamento elástico de débito e de armazenamento](partition-data.md) em todo o mundo, latências de milissegundos de um só dígito no percentil 99 e elevada disponibilidade garantida, tudo com o suporte de [SLA líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa automaticamente os dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem que tenha de lidar com a gestão de esquemas e índices. É multimodal e suporte modelos de dados em documentos, chaves-valores, gráficos e em colunas. Por padrão, você pode interagir com Cosmos DB usando a API do SQL. Além disso, o serviço de Cosmos DB implementa protocolos de conexão para APIs NoSQL comuns, incluindo Cassandra, MongoDB, Gremlin e armazenamento de tabelas do Azure. Isso permite que você use seus drivers e ferramentas de cliente NoSQL familiares para interagir com o banco de dados Cosmos.

## <a name="wire-protocol-compatibility"></a>Compatibilidade de protocolo de conexão

Azure Cosmos DB implementa protocolos de transmissão de bancos de dados NoSQL comuns, incluindo Cassandra, MongoDB, Gremlin e armazenamento de tabelas do Azure. Ao fornecer uma implementação nativa dos protocolos de conexão de forma direta e eficiente dentro de Cosmos DB, ele permite que os SDKs, drivers e ferramentas de cliente existentes dos bancos de dados NoSQL interajam com Cosmos DB de forma transparente. Cosmos DB não usa nenhum código-fonte dos bancos de dados para fornecer APIs compatíveis com cabo para qualquer um dos bancos de dados NoSQL.

Por padrão, novas contas criadas usando a API do Azure Cosmos DB para MongoDB são compatíveis com a versão 3,6 do protocolo de transmissão do MongoDB. Qualquer driver de cliente MongoDB que entenda essa versão de protocolo deve ser capaz de se conectar nativamente a Cosmos DB.

![API do Azure Cosmos DB para MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Principais vantagens

Os principais benefícios do Cosmos DB como um banco de dados como um serviço totalmente gerenciado e distribuído globalmente são descritos [aqui](introduction.md). Além disso, ao implementar nativamente protocolos de conexão de APIs NoSQL populares, o Cosmos DB oferece os seguintes benefícios:

* Migre facilmente seu aplicativo para Cosmos DB enquanto preserva partes significativas da lógica do seu aplicativo.
* Mantenha seu aplicativo portátil e continue a permanecer independente do fornecedor da nuvem.
* Obtenha SLAs de apoio financeiro e líderes do setor para as APIs NoSQL comuns da plataforma de Cosmos DB.
* Dimensione de forma elástica a produtividade e o armazenamento provisionados para seus bancos de dados cosmos com base na sua necessidade e pague apenas pela taxa de transferência e pelo armazenamento de que você precisa. Isso leva a uma economia de custo significativa.
* Distribuição global completa com replicação de vários mestres.

## <a name="cosmos-dbs-api-for-mongodb"></a>API do Cosmos DB para MongoDB

Siga os guias de início rápido para criar uma conta do Azure Cosmos e migre seu aplicativo MongoDB existente para usar Azure Cosmos DB ou criar um novo:

* [Migre um aplicativo Web node. js existente do MongoDB](create-mongodb-nodejs.md).
* [Compilar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do .NET](create-mongodb-dotnet.md)
* [Criar um aplicativo de console usando a API do Azure Cosmos DB para MongoDB e o SDK do Java](create-mongodb-java.md)

## <a name="next-steps"></a>Passos seguintes

Eis alguns sítios por onde começar:

* Siga o tutorial [conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) para saber como obter as informações da cadeia de conexão da conta.
* Siga o tutorial [usar o Studio 3T com Azure Cosmos DB](mongodb-mongochef.md) para saber como criar uma conexão entre o banco de dados Cosmos e o aplicativo MongoDB no estúdio 3T.
* Siga o tutorial [importar dados do MongoDB para Azure Cosmos DB](mongodb-migrate.md) para importar seus dados para um banco de Cosmos.
* Conecte-se a uma conta do cosmos usando o [Robo 3T](mongodb-robomongo.md).
* Saiba como [configurar as preferências de leitura para aplicativos distribuídos globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Observação: Este artigo descreve um recurso do Azure Cosmos DB que fornece a compatibilidade de protocolo de conexão com bancos de dados do MongoDB. A Microsoft não executa bancos de dados do MongoDB para fornecer esse serviço. O Azure Cosmos DB não é afiliado ao MongoDB, Inc.</sup>
