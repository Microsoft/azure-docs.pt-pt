---
title: Compilar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do .NET
description: Apresenta um exemplo de código .NET que você pode usar para se conectar e consultar usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.openlocfilehash: 55f40e8f22b39caa3527a378f33982276bed597d
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755144"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Início rápido: compilar um aplicativo Web .NET usando a API do Azure Cosmos DB para MongoDB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Você pode criar e consultar rapidamente documentos, chave/valor e bancos de dados de grafo, todos beneficiando-se dos recursos de escala horizontal e distribuição global no núcleo de Cosmos DB. 

Este guia de início rápido demonstra como criar uma conta do cosmos com a [API do Azure Cosmos DB para MongoDB](mongodb-introduction.md). Em seguida, você criará e implantará um aplicativo Web de lista de tarefas criado usando o [Driver .net do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Pré-requisitos para executar a aplicação de exemplo

Para executar o exemplo, você precisará do [Visual Studio](https://www.visualstudio.com/downloads/) e de uma conta de Azure Cosmos DB válida.

Se você ainda não tiver o Visual Studio, baixe o [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/) com o ASP.net e a carga de trabalho de **desenvolvimento Web** instaladas com a instalação.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

O exemplo descrito neste artigo é compatível com MongoDB.Driver versão 2.6.1.

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Primeiro, baixe o aplicativo de exemplo do GitHub. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Se ainda não pretende utilizar o git, também pode [transferir o projeto como um ficheiro ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Os seguintes fragmentos são retirados do ficheiro Dal.cs no diretório DAL.

* Inicialize o cliente.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Obter a base de dados e a coleção.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Obter todos os documentos.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Criar uma tarefa e inseri-la na coleção

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Da mesma forma, pode atualizar e eliminar documentos ao utilizar os métodos [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) e [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html). 

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. Na [portal do Azure](https://portal.azure.com/), em sua conta do cosmos, no painel de navegação esquerdo, clique em **cadeia de conexão**e clique em **chaves de leitura/gravação**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o Nome de Utilizador, a Palavra-passe e o Anfitrião para o ficheiro Dal.cs no próximo passo.

2. Abra o ficheiro **Dal.cs** no diretório **DAL**. 

3. Copie o valor de **nome de utilizador** a partir do portal (com o botão Copiar) e faça deste o valor de **nome de utilizador** no ficheiro **Dal.cs**. 

4. Em seguida, copie o valor de **anfitrião** do portal e faça do mesmo o valor de **anfitrião** no ficheiro **Dal.cs**. 

5. Por fim, copie o valor de **palavra-passe** do portal e faça do mesmo o valor de **palavra-passe** no ficheiro **Dal.cs**. 

Agora você atualizou seu aplicativo com todas as informações de que ele precisa para se comunicar com Cosmos DB. 
    
## <a name="run-the-web-app"></a>Executar a aplicação Web

1. No Visual Studio, clique com o botão direito do rato no projeto no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, escreva *MongoDB.Driver*.

3. A partir dos resultados, instale a biblioteca **MongoDB.Driver**. Esta ação instala o pacote MongoDB.Driver, bem como todas as dependências do mesmo.

4. Clique em CTRL + F5 para executar a aplicação. A aplicação é apresentada no browser. 

5. Clique em **Criar** no browser e crie algumas tarefas novas na aplicação de lista de tarefas.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como criar uma conta do cosmos, criar uma coleção e executar um aplicativo de console. Agora você pode importar dados adicionais para o banco de Cosmos. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
