---
title: Configure e use Azure Synapse Link para Azure Cosmos DB (pré-visualização)
description: Saiba como permitir a ligação sinapse para contas DB Azure Cosmos, crie um recipiente com loja analítica ativada, ligue a base de dados Azure Cosmos à sinapse espaço de trabalho e faça consultas.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/31/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: a634743441866c4f1a9f6d634efe0ff9e368b5e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757848"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Configure e use Azure Synapse Link para Azure Cosmos DB (pré-visualização)

[Azure Synapse Link for Azure Cosmos DB](synapse-link.md) é uma capacidade de processamento transacional e analítico híbrido nativo da nuvem (HTAP) que lhe permite executar perto de análises em tempo real sobre dados operacionais em Azure Cosmos DB. A Synapse Link cria uma integração apertada e perfeita entre a Azure Cosmos DB e a Azure Synapse Analytics.

> [!IMPORTANT]
> Para utilizar o Azure Synapse Link, certifique-se de que fornece a sua conta DB Azure Cosmos & espaço de trabalho Azure Synapse Analytics numa das regiões apoiadas. A azure Synapse Link está atualmente disponível nas seguintes regiões de Azure: EUA West Central, East US2, North Europe, West Europe, South Central US, Southeast Asia, Australia East, East U2, UK South.

O Azure Synapse Link está disponível para recipientes Azure Cosmos DB SQL API ou para Azure Cosmos DB API para coleções de DB mongo. Utilize os seguintes passos para executar consultas analíticas com a Ligação Azure Synapse para Azure Cosmos DB:

* [Ativar o Synapse Link para as suas contas DB Azure Cosmos](#enable-synapse-link)
* [Criar uma loja analítica ativada recipiente DB Azure Cosmos](#create-analytical-ttl)
* [Ligue a sua base de dados DB Azure Cosmos a um espaço de trabalho da Synapse](#connect-to-cosmos-database)
* [Consultar a loja analítica usando a Faísca de Sinapse](#query-analytical-store-spark)
* [Consultar a loja analítica usando o sinapse SQL sem servidor](#query-analytical-store-sql-on-demand)
* [Use o sinapse SQL sem servidor para analisar e visualizar dados em Power BI](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Ativar a Ligação Azure Synapse para contas DB do Azure Cosmos

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure,](create-sql-api-dotnet.md#create-account)ou selecione uma conta DB Azure Cosmos existente.

1. Navegue na sua conta DB Azure Cosmos e abra o painel **de funcionalidades.**

1. Selecione **Synapse Link** da lista de funcionalidades.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Encontre a funcionalidade de pré-visualização do Synapse Link":::

1. Em seguida, solicita-lhe que ative o link sinapse na sua conta. Selecione **Ativar**. Este processo pode demorar 1 a 5 minutos a ser concluído.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Encontre a funcionalidade de pré-visualização do Synapse Link":::

1. A sua conta está agora habilitada a utilizar o Synapse Link. Em seguida, veja como criar uma loja analítica que permita aos contentores começarem automaticamente a replicar os seus dados operacionais da loja transacional para a loja analítica.

> [!NOTE]
> Ligar o Synapse Link não liga automaticamente a loja analítica. Assim que ativar o Synapse Link na conta Cosmos DB, ative a loja analítica em contentores quando os criar, para começar a replicar os seus dados de operação para a loja analítica. 

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Criar um recipiente Azure Cosmos com loja analítica

Pode ligar a loja analítica num recipiente Azure Cosmos enquanto cria o recipiente. Você pode usar o portal Azure ou configurar a propriedade durante a `analyticalTTL` criação de contentores usando os SDKs Azure Cosmos.

> [!NOTE]
> Atualmente pode ativar a loja analítica para **novos** contentores (tanto em contas novas como existentes). Pode migrar dados dos seus recipientes de exisiting para novos contentores utilizando [ferramentas de migração Azure Cosmos DB.](cosmosdb-migrationchoices.md)

### <a name="azure-portal"></a>Portal do Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com/) ou no [explorador Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue na sua conta DB Azure Cosmos e abra o **separador Data Explorer.**

1. Selecione **Novo Recipiente** e introduza um nome para a sua base de dados, recipiente, chave de partição e detalhes de saída. Ligue a opção **de loja analítica.** Depois de ativar a loja analítica, cria um recipiente com `AnalyicalTTL` propriedade definida para o valor padrão de -1 (retenção infinita). Esta loja analítica que mantém todas as versões históricas dos registos.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Encontre a funcionalidade de pré-visualização do Synapse Link":::

1. Se não tiver ativado o Synapse Link nesta conta, irá pedir-lhe que o faça porque é um pré-requisito para criar um recipiente analítico habilitado para a loja. Se solicitado, selecione **Enable Synapse Link**. Este processo pode demorar 1 a 5 minutos a ser concluído.

1. Selecione **OK**, para criar uma loja analítica ativada no recipiente Azure Cosmos.

1. Após a criação do contentor, verifique se a loja analítica foi ativada clicando em **Definições**, logo abaixo documentos no Data Explorer, e verifique se a opção **Tempo de Loja Analítica para Viver** está ligada.

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

### <a name="python-v4-sdk"></a>Python V4 SDK

Python 2.7 e Azure Cosmos DB SDK 4.1.0 são as versões mínimas necessárias, e o SDK só é compatível com o SQL API.

O primeiro passo é certificar-se de que está a utilizar pelo menos a versão 4.1.0 do [Azure Cosmos DB Python SDK:](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
O passo seguinte cria um recipiente com loja analítica utilizando o Azure Cosmos DB Python SDK:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Atualize o tempo de loja analítica para viver

Após o arquivo analítico ser ativado com um valor de TTL em particular, pode atualizá-lo posteriormente para um valor válido diferente. Pode atualizar o valor com o portal do Azure ou os SDKs. Para obter informações sobre as várias opções de config Analítico TTL, consulte o artigo [de valores suportados pela TTL analítica.](analytical-store-introduction.md#analytical-ttl)

#### <a name="azure-portal"></a>Portal do Azure

Se criou um recipiente analítico ativado através do portal Azure, contém uma TTL analítica predefinida de -1. Utilize os seguintes passos para atualizar este valor:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) ou no [explorador Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue na sua conta DB Azure Cosmos e abra o **separador Data Explorer.**

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

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Conecte-se a um espaço de trabalho da Sinapse

Utilize as instruções em [Connect to Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sobre como aceder a uma base de dados DB Azure Cosmos do Azure Synapse Analytics Studio com Azure Synapse Link.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Loja analítica de consulta usando Apache Spark para Azure Synapse Analytics

Utilize as instruções no artigo da [loja analítica Da Consulta Azure Cosmos DB](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) sobre como consultar a Synapse Spark. Este artigo dá alguns exemplos sobre como você pode interagir com a loja analítica a partir de gestos da Sinapse. Esses gestos são visíveis quando clicas à direita num contentor. Com gestos, pode gerar rapidamente código e ajustá-lo às suas necessidades. Também são perfeitos para descobrir dados com um único clique.

## <a name="query-the-analytical-store-using-synapse-sql-serverless"></a><a id="query-analytical-store-sql-on-demand"></a> Consultar a loja analítica usando o sinapse SQL sem servidor

Sinaapse SQL serverless (uma funcionalidade de pré-visualização que, anteriormente referida como **SQL on-demand**) permite-lhe consultar e analisar dados nos seus contentores DB Azure Cosmos que estão habilitados com Azure Synapse Link. Pode analisar dados em quase tempo real sem afetar o desempenho das suas cargas de trabalho transacionais. Oferece uma sintaxe T-SQL familiar para consultar dados da loja analítica e conectividade integrada a uma ampla gama de ferramentas de consulta bi e ad-hoc através da interface T-SQL. Para saber mais, consulte a loja de analítica De consulta utilizando o artigo [sem servidor Synapse SQL.](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

> [!NOTE]
> A utilização da loja analítica Azure Cosmos DB com servidor Sinaapse SQL está atualmente sob pré-visualização fechada. Para solicitar acesso, contacte a equipa DB da [Azure Cosmos.](mailto:cosmosdbsynapselink@microsoft.com)

## <a name="use-synapse-sql-serverless-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Use o sinapse SQL sem servidor para analisar e visualizar dados em Power BI

Pode construir uma base de dados sem servidor Sinaapse SQL e visualizações sobre a Synapse Link para Azure Cosmos DB. Mais tarde, você pode consultar os contentores Azure Cosmos e, em seguida, construir um modelo com Power BI sobre essas vistas para refletir essa consulta. Para saber mais, veja como usar [o sinapse SQL sem servidor para analisar os dados do Azure Cosmos DB com o artigo Synapse Link.](synapse-link-power-bi.md)

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

O [modelo Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) cria uma conta DB do Azure Cosmos habilitada para a API SQL. Este modelo cria uma conta API core (SQL) numa região com um recipiente configurado com TTL analítico ativado, e uma opção de utilização manual ou de escala automática. Para implementar este modelo, clique em **Implementar para Azure** na página de readme.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Começando com Azure Synpase Link - Amostras

Pode encontrar amostras para começar com a Azure Synapse Link no [GitHub.](https://aka.ms/cosmosdb-synapselink-samples) Estas soluções de ponta a ponta com cenários IoT e retail. Também pode encontrar as amostras correspondentes à Azure Cosmos DB API para a MongoDB no mesmo repo sob a pasta [MongoDB.](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/MongoDB) 

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes documentos:

* [Ligação Azure Synapse para Azure Cosmos DB.](synapse-link.md)

* [Descrição geral do arquivo analítico do Azure Cosmos DB.](analytical-store-introduction.md)

* [Perguntas frequentes sobre Synapse Link para Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Faísca Apache em Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Suporte de tempo sem servidor SQL no Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
