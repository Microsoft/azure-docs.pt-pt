---
title: Configure e utilize o Link Synapse Azure para Azure Cosmos DB (pré-visualização)
description: Saiba como permitir a ligação sinapse para as contas da Azure Cosmos, crie um recipiente com loja analítica habilitada, ligue a base de dados Azure Cosmos ao espaço de trabalho sinapse e ecorra consultas.
author: SriChintala
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: 24f6f77f1371157ca1b57cfd85ac196cace822fc
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83675996"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Configure e utilize o Link Synapse Azure para Azure Cosmos DB (pré-visualização)

Synapse Link for Azure Cosmos DB é uma capacidade de processamento transalítico e analítico híbrido nativo-nuvem (HTAP) que lhe permite executar análises em tempo real sobre dados operacionais em Azure Cosmos DB. A Synapse Link cria uma integração apertada e perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics.


> [!IMPORTANT]
> Para utilizar o Azure Synapse Link, certifique-se de fornecer a sua conta Azure Cosmos & espaço de trabalho Azure Synapse Analytics numa das regiões suportadas acima. Para a lista de regiões apoiadas, consulte as atualizações de [serviço do Azure.](https://azure.microsoft.com/updates/) 

Utilize os seguintes passos para executar consultas analíticas com o Synapse Link para Azure Cosmos DB:

* [Enable Synapse Link para as suas contas Azure Cosmos](#enable-synapse-link)
* [Criar uma loja analítica habilitada para o recipiente Azure Cosmos](#create-analytical-ttl)
* [Ligue a sua base de dados Azure Cosmos a um espaço de trabalho Synapse](#connect-to-cosmos-database)
* [Consulta da loja analítica usando Synapse Spark](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Enable Azure Synapse Link para contas Azure Cosmos

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Navegue para a sua conta Azure Cosmos e abra o painel **Features.**

1. Selecione **Synapse Link** na lista de funcionalidades.

   ![Encontre a funcionalidade de pré-visualização do Synapse Link](./media/configure-synapse-link/find-synapse-link-feature.png)

1. Em seguida, solicita-lhe que ative o link sinapse na sua conta. Selecione Ativar.

   ![Ativar a funcionalidade Synapse Link](./media/configure-synapse-link/enable-synapse-link-feature.png)

1. A sua conta está agora habilitada a utilizar o Synapse Link. Em seguida, veja como criar recipientes analíticos habilitados para começar automaticamente a replicar os seus dados operacionais da loja transacional para a loja analítica.

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

O modelo do Gestor de [Recursos Azure](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) cria uma conta synapse Link ativada pela Azure Cosmos para a SQL API. Este modelo cria uma conta API Core (SQL) numa região com um recipiente configurado com TTL analítico habilitado, e uma opção de utilização manual ou de entrada de escala automática. Para implementar este modelo, clique em **Deploy para Azure** na página readme.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a>Crie um recipiente Azure Cosmos com loja analítica

Pode ligar a loja analítica num recipiente Azure Cosmos enquanto cria o recipiente. Pode utilizar o portal Azure ou configurar a propriedade durante a criação de `analyticalTTL` contentores utilizando os SDKs DB Azure Cosmos.

> [!NOTE]
> Atualmente pode ativar a loja analítica para **novos** contentores (tanto em contas novas como existentes).

### <a name="azure-portal"></a>Portal do Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com/) ou no [explorador Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue para a sua conta Azure Cosmos e abra o separador **Data Explorer.**

1. Selecione **New Container** e introduza um nome para a sua base de dados, recipiente, chave de partição e detalhes de entrada. Ligue a opção loja **Analítica.** Depois de ativar a loja analítica, cria um recipiente com `AnalyicalTTL` propriedade definida para o valor padrão de -1 (retenção infinita). Esta loja analítica que retém todas as versões históricas dos registos.

   ![Ligue a loja analítica para o contentor Azure Cosmos](./media/configure-synapse-link/create-container-analytical-store.png)

1. Se não tiver ativado previamente o Synapse Link nesta conta, irá pedir-lhe que o faça porque é um pré-requisito para criar um recipiente analítico habilitado para armazenar. Se for solicitado, **selecione Enable Synapse Link**.

1. Selecione **OK,** para criar um recipiente azure cosmos ativado por uma loja analítica.

### <a name="net-sdk"></a>SDK .NET

O seguinte código cria um recipiente com armazenação analítica utilizando o .NET SDK. Deteto a propriedade TTL analítica ao valor exigido. Para a lista de valores permitidos, consulte o artigo [valores apoiados pela TTL analítico:](analytical-store-introduction.md#analytical-ttl)

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

O seguinte código cria um recipiente com loja analítica utilizando o Java V4 SDK. Desloque a `AnalyticalStoreTimeToLiveInSeconds` propriedade ao valor exigido:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v3-sdk"></a>Python V3 SDK

O seguinte código cria um recipiente com loja analítica utilizando o Python SDK:

```python
import azure.cosmos.cosmos_client as cosmos_client
def create_collection_if_not_exists(cosmosEndpoint, cosmosKey, databaseName, collectionName):
    client = cosmos_client.CosmosClient(url_connection=cosmosEndpoint, auth={'masterKey': cosmosKey})

db = client.QueryDatabases("select * from c where c.id = '" + databaseName + "'").fetch_next_block()[0]
options = {
    'offerThroughput': 1000
}

container_definition = {
    'id': collectionName,
    "partitionKey": {  
        "paths": [  
        "/id"  
        ],  
        "kind": "Hash" 
    },
    "indexingPolicy": {  
    "indexingMode": "consistent",  
    "automatic": True,  
    "includedPaths": [],  
    "excludedPaths": [{
        "path": "/*"
    }]  
    },
    "defaultTtl": -1,
    "analyticalStorageTtl": -1
}

container = client.CreateContainer(db['_self'], container_definition, options)
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a>Atualize o tempo da loja analítica para viver

Depois de a loja analítica estar ativada com um valor TTL específico, pode atualizá-la para um valor válido diferente mais tarde. Pode atualizar o valor utilizando o portal Azure ou SDKs. Para obter informações sobre as várias opções de config Analítica TTL, consulte o artigo de [valores suportados analíticos TTL.](analytical-store-introduction.md#analytical-ttl)

#### <a name="azure-portal"></a>Portal do Azure

Se criou um recipiente analítico ativado através do portal Azure, contém um TTL analítico padrão de -1. Utilize os seguintes passos para atualizar este valor:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) ou no [explorador Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue para a sua conta Azure Cosmos e abra o separador **Data Explorer.**

1. Selecione um recipiente existente que tenha uma loja analítica ativada. Expanda-o e modifique os seguintes valores:

  * Abra a janela **Definições de escala &.**
  * Em **Definição** encontre,** Tempo de armazenamento analítico para viver**.
  * Selecione **On (sem predefinição)** ou selecione **On** e definir um valor TTL
  * Clique em **Guardar** para guardar as alterações.

#### <a name="net-sdk"></a>SDK .NET

O seguinte código mostra como atualizar o TTL para a loja analítica utilizando o .NET SDK:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java V4 SDK

O seguinte código mostra como atualizar o TTL para a loja analítica utilizando o Java V4 SDK:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a>Ligue-se a um espaço de trabalho synapse

Utilize as instruções em [Connect to Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sobre como aceder a uma base de dados Azure Cosmos DB do Azure Synapse Analytics Studio com Azure Synapse Link.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a>Consulta usando Faísca Synapse

Utilize as instruções no artigo da [loja analítica Query Azure Cosmos DB](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) sobre como consultar a Synapse Spark. Este artigo dá alguns exemplos de como pode interagir com a loja analítica a partir de gestos synapse. Esses gestos são visíveis quando clica à direita num recipiente. Com gestos, pode gerar código rapidamente e ajustá-lo às suas necessidades. Também são perfeitos para descobrir dados com um único clique.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Começar com Ligação Synpase Azure - Amostras

Pode encontrar amostras para começar com o Azure Synapse Link no [Github](https://aka.ms/cosmosdb-synapselink-samples). Estas soluções de ponta a ponta com cenários IoT e Retail.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes docs:

* [Ligação Azure Synapse para Azure Cosmos DB.](synapse-link.md)

* [Visão geral da loja analítica Azure Cosmos DB.](analytical-store-introduction.md)

* [Perguntas frequentes sobre synapse Link para Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark em Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [SQL sem servidor/on-demand em Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
