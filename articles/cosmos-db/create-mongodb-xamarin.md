---
title: Construa uma app Xamarin com API da AZure Cosmos para a MongoDB
description: Apresenta uma amostra de código Xamarin que pode usar para ligar e consultar a API da Azure Cosmos DB para o MongoDB
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: masoucou
ms.custom: devx-track-csharp
ms.openlocfilehash: 75db62b4f8a5c6512ca5fc84d149513fe81f6019
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658236"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>QuickStart: Construa uma app Xamarin.Forms com API da .NET SDK e Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB.

Este quickstart demonstra como criar uma [conta Cosmos configurada com a API da Azure Cosmos DB para o MongoDB,](mongodb-introduction.md)base de dados de documentos e recolha utilizando o portal Azure. Em seguida, você construirá uma aplicação toda app Xamarin.Forms usando o [controlador MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Pré-requisitos para executar a aplicação de exemplo

Para executar o exemplo, precisa do [Visual Studio](https://www.visualstudio.com/downloads/) ou do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e de uma conta válida do Azure CosmosDB.

Se ainda não tem Visual Studio, baixe [o Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) com o desenvolvimento mobile com a carga de trabalho **.NET** instalada com configuração.

Se preferir trabalhar num Mac, transfira o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute a configuração.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

O exemplo descrito neste artigo é compatível com MongoDB.Driver versão 2.6.1.

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Primeiro, descarregue a aplicação de amostras do GitHub. Esta implementa uma aplicação de tarefas com o modelo de armazenamento de documentos do MongoDB.



# <a name="windows"></a>[Windows](#tab/windows)

1. No Windows abra um pedido de comando ou no Mac abra o terminal, crie uma nova pasta chamada amostras de git e feche a janela.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Se você não deseja usar git, você também pode [baixar o projeto como um arquivo ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string).

Os seguintes excertos são todos retirados da `MongoService` classe, encontrados no seguinte caminho: src/TaskList.Core/Services/MongoService.cs.

* Inicializar o cliente Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(new MongoUrl(APIKeys.ConnectionString));

    settings.SslSettings = new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Obtenha uma referência para a base de dados e a coleção. O SDK .NET do MongoDB irá criar automaticamente a base de dados e a coleção se estes ainda não existirem.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings 
    {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Obter todos os documentos como uma Lista.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Consulta de documentos específicos.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Crie uma tarefa e insira-a na coleção.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Atualize uma tarefa numa coleção.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Elimine uma tarefa de uma coleção.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](https://portal.azure.com/), na sua conta do Azure Cosmos DB, na navegação da esquerda, clique em **Cadeia de Ligação** e em **Chaves de leitura/escrita**. Irá utilizar os botões de cópia à direita do ecrã para copiar a Cadeia de Ligação Primária nos próximos passos.

2. Abra o ficheiro **APIKeys.cs** no diretório **Programas Auxiliares** do projeto **TaskList.Core**.

3. Copie o seu valor da **cadeia de ligação primária** do portal (com o botão de cópia) e torne-o o valor do campo **ConnectionString** no seu ficheiro **APIKeys.cs**.

4. `&replicaSet=globaldb`Retire da cadeia de ligação. Obterá um erro de tempo de execução se não remover esse valor da cadeia de consulta.

> [!IMPORTANT]
> Deve remover o `&replicaSet=globaldb` par de tecla/valor da cadeia de consulta da cadeia de ligação para evitar um erro de tempo de execução.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB.

## <a name="run-the-app"></a>Executar a aplicação

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. No Visual Studio, clique com o botão direito do rato em cada projeto no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**.
2. Clique em **Restaurar todos os pacotes NuGet**.
3. Clique com o botão direito do rato em **TaskList.Android** e selecione **Definir como projeto de arranque**.
4. Prima F5 para iniciar a depuração da aplicação.
5. Se quiser executar em iOS, primeiro o seu computador tem de estar ligado a um Mac (eis as [instruções](/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) sobre como fazê-lo).
6. Clique com o botão direito do rato no projeto **TaskList.iOS** e selecione **Definir como projeto de arranque**.
7. Clique em F5 para iniciar a depuração da aplicação.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Na lista pendente da plataforma, selecione TaskList.iOS ou TaskList.Android, dependendo da plataforma que pretende executar.
2. Prima cmd+Enter para iniciar a depuração da aplicação.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a criar uma conta do Azure Cosmos DB e a executar uma aplicação Xamarin.Forms com a API do MongoDB. Agora, pode importar dados adicionais à sua conta do Cosmos DB.

> [!div class="nextstepaction"]
> [Dados de importação para Azure Cosmos DB configurados com API da Azure Cosmos DB para a MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)