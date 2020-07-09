---
title: Configure e use Azure Synapse Link para Azure Cosmos DB (pré-visualização)
description: Saiba como permitir a ligação sinapse para as contas do Azure Cosmos, crie um recipiente com loja analítica ativada, ligue a base de dados do Azure Cosmos à sinapse espaço de trabalho e faça consultas.
author: SriChintala
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: d2a10d064bed3e2e2e798d16ce72ccf55c965f8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262043"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Configure e use Azure Synapse Link para Azure Cosmos DB (pré-visualização)

Synapse Link for Azure Cosmos DB é uma capacidade de processamento transacional e analítico híbrido nativo da nuvem (HTAP) que lhe permite executar análises em tempo real sobre dados operacionais em Azure Cosmos DB. A Synapse Link cria uma integração apertada e perfeita entre a Azure Cosmos DB e a Azure Synapse Analytics.


> [!IMPORTANT]
> Para utilizar o Azure Synapse Link, certifique-se de que fornece a sua conta Azure Cosmos & espaço de trabalho Azure Synapse Analytics numa das regiões acima apoiadas. Para a lista de regiões apoiadas, consulte [as atualizações do serviço Azure](https://azure.microsoft.com/updates/). 

Utilize os seguintes passos para executar consultas analíticas com a Ligação Synapse para Azure Cosmos DB:

* [Ativar o Synapse Link para as suas contas Azure Cosmos](#enable-synapse-link)
* [Criar uma loja analítica ativada recipiente Azure Cosmos](#create-analytical-ttl)
* [Ligue a sua base de dados Azure Cosmos a um espaço de trabalho da Sinapse](#connect-to-cosmos-database)
* [Consultar a loja analítica usando a Faísca de Sinapse](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Ativar a Ligação Azure Synapse para contas Azure Cosmos

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Navegue na sua conta Azure Cosmos e abra o painel **de funcionalidades.**

1. Selecione **Synapse Link** da lista de funcionalidades.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Encontre a funcionalidade de pré-visualização do Synapse Link":::

1. Em seguida, solicita-lhe que ative o link sinapse na sua conta. Selecione Ativar.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Ativar a funcionalidade Synapse Link":::

1. A sua conta está agora habilitada a utilizar o Synapse Link. Em seguida, veja como criar uma loja analítica que permita aos contentores começarem automaticamente a replicar os seus dados operacionais da loja transacional para a loja analítica.

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

O [modelo Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) cria uma conta de Link Synapse ativada a Azure Cosmos para a SQL API. Este modelo cria uma conta API core (SQL) numa região com um recipiente configurado com TTL analítico ativado, e uma opção de utilização manual ou de escala automática. Para implementar este modelo, clique em **Implementar para Azure** na página de readme.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a>Criar um recipiente Azure Cosmos com loja analítica

Pode ligar a loja analítica num recipiente Azure Cosmos enquanto cria o recipiente. Você pode usar o portal Azure ou configurar a propriedade durante a `analyticalTTL` criação de contentores usando os SDKs Azure Cosmos.

> [!NOTE]
> Atualmente pode ativar a loja analítica para **novos** contentores (tanto em contas novas como existentes).

### <a name="azure-portal"></a>Portal do Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com/) ou no [explorador Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue na sua conta Azure Cosmos e abra o **separador Data Explorer.**

1. Selecione **Novo Recipiente** e introduza um nome para a sua base de dados, recipiente, chave de partição e detalhes de saída. Ligue a opção **de loja analítica.** Depois de ativar a loja analítica, cria um recipiente com `AnalyicalTTL` propriedade definida para o valor padrão de -1 (retenção infinita). Esta loja analítica que mantém todas as versões históricas dos registos.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Ligue a loja analítica para o recipiente Azure Cosmos":::

1. Se não tiver ativado o Synapse Link nesta conta, irá pedir-lhe que o faça porque é um pré-requisito para criar um recipiente analítico habilitado para a loja. Se solicitado, selecione **Enable Synapse Link**.

1. Selecione **OK**, para criar uma loja analítica ativada no recipiente Azure Cosmos.

### <a name="net-sdk"></a>SDK .NET

O seguinte código cria um recipiente com loja analítica utilizando o .NET SDK. Desaperte a propriedade TTL analítica ao valor exigido. Para obter a lista de valores permitidos, consulte o artigo [de valores suportados pela TTL analítica:](analytical-store-introduction.md#analytical-ttl)

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

O seguinte código cria um recipiente com loja analítica utilizando o Java V4 SDK. Desau o `AnalyticalStoreTimeToLiveInSeconds` valor necessário:

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

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a>Atualize o tempo de loja analítica para viver

Após o arquivo analítico ser ativado com um valor de TTL em particular, pode atualizá-lo posteriormente para um valor válido diferente. Pode atualizar o valor com o portal do Azure ou os SDKs. Para obter informações sobre as várias opções de config Analítico TTL, consulte o artigo [de valores suportados pela TTL analítica.](analytical-store-introduction.md#analytical-ttl)

#### <a name="azure-portal"></a>Portal do Azure

Se criou um recipiente analítico ativado através do portal Azure, contém uma TTL analítica predefinida de -1. Utilize os seguintes passos para atualizar este valor:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) ou no [explorador Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue na sua conta Azure Cosmos e abra o **separador Data Explorer.**

1. Selecione um recipiente existente que tenha a loja analítica ativada. Expanda-o e modifica os seguintes valores:

  * Abra a **janela 'Definições & escala'.**
  * Em **Definição** encontrar,** Tempo de armazenamento analítico para viver**.
  * Selecione **On (sem predefinição)** ou selecione **On** e desafina um valor TTL
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

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a>Conecte-se a um espaço de trabalho da Sinapse

Utilize as instruções em [Connect to Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sobre como aceder a uma base de dados DB Azure Cosmos do Azure Synapse Analytics Studio com Azure Synapse Link.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a>Consulta usando faísca de sinapse

Utilize as instruções no artigo da [loja analítica Da Consulta Azure Cosmos DB](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) sobre como consultar a Synapse Spark. Este artigo dá alguns exemplos sobre como você pode interagir com a loja analítica a partir de gestos da Sinapse. Esses gestos são visíveis quando clicas à direita num contentor. Com gestos, pode gerar rapidamente código e ajustá-lo às suas necessidades. Também são perfeitos para descobrir dados com um único clique.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Começando com Azure Synpase Link - Amostras

Pode encontrar amostras para começar com a Azure Synapse Link no [GitHub.](https://aka.ms/cosmosdb-synapselink-samples) Estas soluções de ponta a ponta com cenários IoT e Retail.

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os seguintes documentos:

* [Ligação Azure Synapse para Azure Cosmos DB.](synapse-link.md)

* [Descrição geral do arquivo analítico do Azure Cosmos DB.](analytical-store-introduction.md)

* [Perguntas frequentes sobre Synapse Link para Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Faísca Apache em Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [SQL sem servidor/a pedido no Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
