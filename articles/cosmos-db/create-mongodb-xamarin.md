---
title: Criar uma aplicação xamarin. Forms com .NET e do Azure Cosmos DB API para MongoDB
description: Apresenta um exemplo de código Xamarin, que pode utilizar para ligar e consultar com a API do Azure Cosmos DB para o MongoDB
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: c7a80ae704d13934a5d51bc5538bc28693b49e5b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588097"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Início rápido: Criar uma aplicação xamarin. Forms com o SDK do .NET e do Azure Cosmos DB API para MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB.

Este início rápido demonstra como criar uma [conta do Cosmos configurada com a API do Azure Cosmos DB para o MongoDB](mongodb-introduction.md), base de dados de documentos e coleções com o portal do Azure. Em seguida, irá criar uma aplicação xamarin. Forms a fazer aplicações utilizando o [controlador .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Pré-requisitos para executar a aplicação de exemplo

Para executar o exemplo, precisa do [Visual Studio](https://www.visualstudio.com/downloads/) ou do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e de uma conta válida do Azure CosmosDB.

Se ainda não tem o Visual Studio, transfira o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) com a carga de trabalho do **Desenvolvimento móvel com .NET** instalada com a configuração.

Se preferir trabalhar num Mac, transfira o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute a configuração.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

O exemplo descrito neste artigo é compatível com MongoDB.Driver versão 2.6.1.

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Em primeiro lugar, transfira a aplicação de exemplo do GitHub. Esta implementa uma aplicação de tarefas com o modelo de armazenamento de documentos do MongoDB.

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Se ainda não pretende utilizar o git, também pode [transferir o projeto como um ficheiro ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string).

Os seguintes fragmentos são retirados do `MongoService` classe, encontrada no seguinte caminho: src/TaskList.Core/Services/MongoService.cs.

* Inicializar o cliente Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Obtenha uma referência para a base de dados e a coleção. O SDK .NET do MongoDB irá criar automaticamente a base de dados e a coleção se estes ainda não existirem.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
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

* Criar uma tarefa e inseri-lo na coleção.
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

* Elimine uma tarefa a partir de uma coleção.
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

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB.

## <a name="run-the-app"></a>Executar a aplicação

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. No Visual Studio, clique com o botão direito do rato em cada projeto no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**.
2. Clique em **Restaurar todos os pacotes NuGet**.
3. Clique com o botão direito do rato em **TaskList.Android** e selecione **Definir como projeto de arranque**.
4. Prima F5 para iniciar a depuração da aplicação.
5. Se quiser executar em iOS, primeiro o seu computador tem de estar ligado a um Mac (eis as [instruções](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) sobre como fazê-lo).
6. Clique com o botão direito do rato no projeto **TaskList.iOS** e selecione **Definir como projeto de arranque**.
7. Clique em F5 para iniciar a depuração da aplicação.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Na lista pendente da plataforma, selecione TaskList.iOS ou TaskList.Android, dependendo da plataforma que pretende executar.
2. Prima cmd+Enter para iniciar a depuração da aplicação.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar uma conta do Azure Cosmos DB e a executar uma aplicação Xamarin.Forms com a API do MongoDB. Agora, pode importar dados adicionais à sua conta do Cosmos DB.

> [!div class="nextstepaction"]
> [Importar dados para o Azure Cosmos DB, se for configurado com a API do Azure Cosmos DB para o MongoDB](mongodb-migrate.md)
