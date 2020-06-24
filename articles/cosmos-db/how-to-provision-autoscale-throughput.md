---
title: Provisão de produção de escala automática em Azure Cosmos DB
description: Saiba como providenciar a produção de autoescalação no nível do contentor e da base de dados em Azure Cosmos DB utilizando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: 59feabce099087edb011df471561229bfa88a289
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118734"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db"></a>Provisão de produção automática na base de dados ou contentor em Azure Cosmos DB

Este artigo explica como providenciar a produção de autoescalação numa base de dados ou contentor (recolha, gráfico ou tabela) em Azure Cosmos DB. Pode ativar a autoescala num único contentor ou providenciar a produção de autoescala numa base de dados e partilhá-la entre todos os contentores da base de dados. 

## <a name="azure-portal"></a>Portal do Azure

### <a name="create-new-database-or-container-with-autoscale"></a>Criar nova base de dados ou contentor com autoescala
1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [explorador DB do Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue na sua conta DB Azure Cosmos e abra o **separador Data Explorer.**

1. Selecione **Novo Recipiente.** Insira um nome para a sua base de dados, recipiente e uma chave de partição. No **Âmbito do 'Under Throughput**' ' '' selecione a opção **de autoescala** e desagreda a [potência máxima (RU/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) a que pretende que a base de dados ou o recipiente se dimensionem.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Criação de um contentor e configuração de produção de autoescala":::

1. Selecione **OK**.

Para obter uma escala automática na base de dados de produção partilhada, selecione a opção **de produção de base de dados de provisão de Provisão** ao criar uma nova base de dados. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Permitir a escala automática na base de dados ou no recipiente existente

> [!IMPORTANT]
> Na versão atual, o portal Azure é a única forma de migrar entre a produção de autoescalação e a produção normalizada (manual). 

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [explorador DB do Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue na sua conta DB Azure Cosmos e abra o **separador Data Explorer.**

1. Selecione **Escala e Definições** para o seu recipiente ou **Escala** para a sua base de dados.

1. Em **Escala**, selecione a opção **Autoscale** e **Guarde**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Permitir a autoescalação num recipiente existente":::

> [!NOTE]
> Quando ativa automaticamente uma base de dados ou um recipiente existente, o valor inicial para max RU/s é determinado pelo sistema, com base nas definições de produção e armazenamento atuais. Após o funcionamento concluído, pode alterar o máximo RU/s, se necessário. [Saiba mais.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk-for-sql-api"></a>Azure Cosmos DB .NET V3 SDK para SQL API
Utilize a [versão 3.9 ou superior](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do Azure Cosmos DB .NET SDK para a SQL API para gerir recursos de autoescala. 

> [!IMPORTANT]
> Pode utilizar o .NET SDK para criar novos recursos de autoescala. O SDK não suporta a migração entre a escala automática e a produção normalizada (manual). O cenário de migração é atualmente suportado apenas no portal Azure. 

### <a name="create-database-with-shared-throughput"></a>Criar base de dados com produção partilhada
```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Criar recipiente com produção dedicada
```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Leia a produção atual (RU/s)
```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Alterar a potência máxima de autoescala (RU/s)
```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk-for-sql-api"></a>Azure Cosmos DB Java V4 SDK para API SQL
Pode utilizar a [versão 4.0 ou superior](https://mvnrepository.com/artifact/com.azure/azure-cosmos) do Azure Cosmos DB Java SDK para a SQL API gerir recursos de autoescala. 

> [!IMPORTANT]
> Pode utilizar o SDK Java para criar novos recursos de autoescala. O SDK não suporta a migração entre a escala automática e a produção normalizada (manual). O cenário de migração é atualmente suportado apenas no portal Azure. 

### <a name="create-database-with-shared-throughput"></a>Criar base de dados com produção partilhada

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Sincronização](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Criar recipiente com produção dedicada

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Sincronização](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Leia a produção atual (RU/s)

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Sincronização](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Alterar a potência máxima de autoescala (RU/s)

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Sincronização](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

--- 

## <a name="cassandra-api"></a>API de Cassandra 
Consulte este artigo sobre [como utilizar comandos CQL](manage-scale-cassandra.md#use-autoscale) para permitir a autoescala.

## <a name="azure-cosmos-db-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB 
Consulte este artigo sobre [como utilizar comandos de extensão MongoDB](mongodb-custom-commands.md) para permitir a autoescala.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Pode utilizar um modelo de Gestor de Recursos para obter uma produção de escala automática numa base de dados ou num recipiente para qualquer API. Consulte este [artigo](manage-sql-with-resource-manager.md#azure-cosmos-account-with-autoscale-throughput) para obter uma amostra.

## <a name="next-steps"></a>Passos seguintes
* Conheça os [benefícios da produção aussitada com autoescala.](provision-throughput-autoscale.md#benefits-of-autoscale)
* Saiba como escolher entre a [produção manual e autoescala.](how-to-choose-offer.md)
* Reveja as [FAQ de escala automática.](autoscale-faq.md)
