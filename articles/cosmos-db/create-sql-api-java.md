---
title: Quickstart - Use Java para criar uma base de dados de documentos usando Azure Cosmos DB
description: Este quickstart apresenta uma amostra de código Java que pode usar para ligar e consultar o API API AZURE Cosmos DB SQL
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090414"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Quickstart: Construa uma app Java para gerir dados da Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK v4 de Java](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste quickstart, você cria e gere uma conta API API AZURE Cosmos DB SQL a partir do portal Azure, e usando uma aplicação Java clonada do GitHub. Primeiro, cria uma conta Azure Cosmos DB SQL API utilizando o portal Azure, depois cria uma aplicação Java utilizando o SQL Java SDK e, em seguida, adiciona recursos à sua conta Cosmos DB utilizando a aplicação Java. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

> [!IMPORTANT]  
> Este quickstart é apenas para Azure Cosmos DB Java SDK v4. Por favor, veja as notas de lançamento Azure Cosmos DB Java SDK [v4,](sql-api-sdk-java-v4.md) [repositório Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4 dicas de [desempenho](performance-tips-java-sdk-v4-sql.md), e Azure Cosmos DB Java SDK v4 [guia de resolução de problemas](troubleshoot-java-sdk-v4-sql.md) para obter mais informações. Se está a utilizar uma versão mais antiga do que v4, consulte o guia [Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure. Também pode utilizar o [Emulador Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Kit de Desenvolvimento de Java (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a `JAVA_HOME` variável ambiente para a pasta onde o JDK está instalado.
- Um [arquivo binário de Maven.](https://maven.apache.org/download.cgi) Em Ubuntu, corra `apt-get install maven` para instalar Maven.
- [Git.](https://www.git-scm.com/downloads) Em Ubuntu, corra `sudo apt-get install git` para instalar Git.

## <a name="introductory-notes"></a>Notas introdutórias

*A estrutura de uma conta de Cosmos DB.* Independentemente da API ou da linguagem de programação, uma *conta* Cosmos DB contém zero ou mais *bases de dados,* uma base *de dados* (DB) contém zero ou mais *contentores*, e um *recipiente* contém zero ou mais itens, como mostra o diagrama abaixo:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entidades de conta Azure Cosmos" border="false":::

Pode ler mais sobre bases de dados, contentores e itens [aqui.](account-databases-containers-items.md) Algumas propriedades importantes são definidas ao nível do recipiente, entre *elas a chave de produção* e *partição.* 

A produção prevista é medida nas Unidades de Pedido *(RUs)* que têm um preço monetário e constituem um fator determinante substancial no custo de exploração da conta. A produção a provisionada pode ser selecionada em granularidade por contentor ou granularidade por base de dados, no entanto, a especificação de produção ao nível do contentor é normalmente preferível. Pode ler mais sobre o fornecimento de produção [aqui.](set-throughput.md)

À medida que os itens são inseridos num recipiente Cosmos DB, a base de dados cresce horizontalmente adicionando mais armazenamento e cálculo para lidar com pedidos. A capacidade de armazenamento e computação são adicionadas em *unidades discretas conhecidas como divisórias,* e deve escolher um campo nos seus documentos para ser a chave de partição que mapeia cada documento para uma partição. A forma como as divisórias são geridas é que cada divisória é atribuída a uma fatia aproximadamente igual fora da gama de valores-chave de partição; por isso, é aconselhável escolher uma chave de partição que seja relativamente aleatória ou distribuída uniformemente. Caso contrário, algumas divisórias verão substancialmente mais pedidos (*partição quente*), enquanto outras divisórias vêem substancialmente menos pedidos (*partição fria),* e isso deve ser evitado. Pode aprender mais sobre a partilha [aqui.](partitioning-overview.md)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta de API SQL com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contentor

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação da SQL API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar para [Executar a aplicação](#run-the-app). 


# <a name="sync-api"></a>[Sincronização API](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Gestão de recursos de base de dados utilizando a API sincronizada (sincronizada)

* `CosmosClient` inicialização. O `CosmosClient` serviço de base de dados Azure Cosmos fornece representação lógica do lado do cliente. Este cliente é utilizado para configurar e executar pedidos no serviço.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` criação.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` criação.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Criação de artigo utilizando o `createItem` método.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* As leituras de pontos são efetuadas utilizando `readItem` o método.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* As consultas SQL sobre JSON são realizadas usando o `queryItems` método.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[API assínc](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Gestão de recursos de base de dados utilizando a API assíncrono (assíncrono)

* As chamadas da API async regressam imediatamente, sem esperar por uma resposta do servidor. À luz disto, os seguintes snippets de código mostram padrões de design adequados para realizar todas as tarefas de gestão anteriores usando a API async.

* `CosmosAsyncClient` inicialização. O `CosmosAsyncClient` serviço de base de dados Azure Cosmos fornece representação lógica do lado do cliente. Este cliente é utilizado para configurar e executar pedidos assíncronos contra o serviço.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` criação.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` criação.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Tal como acontece com a API sincronizada, a criação de artigos é realizada utilizando o `createItem` método. Este exemplo mostra como emitir eficientemente numerosos pedidos de async, `createItem` subscrevendo um Fluxo Reativo que emite os pedidos e as notificações de impressão. Uma vez que este exemplo simples vai até à conclusão e termina, `CountDownLatch` são usadas instâncias para garantir que o programa não termina durante a criação do artigo. **A prática adequada de programação assíncronia não é bloquear as chamadas async - em casos de utilização realistas, os pedidos são gerados a partir de um loop principal que executa indefinidamente, eliminando a necessidade de travar as chamadas de async.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Tal como acontece com a API sincronizada, as leituras de pontos são efetuadas utilizando `readItem` o método.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Tal como acontece com a API sincronizada, as consultas SQL sobre JSON são realizadas utilizando o `queryItems` método.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>Executar a aplicação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e inicie a aplicação com as suas informações de ponto final. Isto permite à aplicação comunicar com a base de dados alojada.

1. Na janela de terminal do git, `cd` para a pasta de código de exemplo.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. Na janela de terminal do git, utilize o seguinte comando para instalar os pacotes Java necessários.

    ```bash
    mvn package
    ```

3. Na janela do terminal de git, utilize o seguinte comando para iniciar a aplicação Java (substitua o SYNCASYNCMODE com `sync` ou `async` dependendo do código de amostra que pretende executar, substitua YOUR_COSMOS_DB_HOSTNAME pelo valor URI citado do portal e substitua YOUR_COSMOS_DB_MASTER_KEY pela chave primária citada do portal)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A janela do terminal apresenta uma notificação que a base de dados FamilyDB tinha criado. 
    
4. A aplicação cria base de dados com nome `AzureSampleFamilyDB`
5. A aplicação cria recipiente com nome `FamilyContainer`
6. A aplicação executará leituras de ponto usando IDs de objeto e valor chave de partição (que é o último Nome na nossa amostra). 
7. A aplicação irá consultar itens para recuperar todas as famílias com apelido em ('Andersen', 'Wakefield', 'Johnson')

7. A aplicação não elimina os recursos criados. Mude novamente para o portal para [limpar os recursos](#clean-up-resources).  da sua conta, para que não incorra em custos.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta AZure Cosmos DB SQL API, criar uma base de dados de documentos e contentor usando o Data Explorer, e executar uma aplicação Java para fazer a mesma coisa programáticamente. Pode agora importar dados adicionais na sua conta DB Azure Cosmos. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
