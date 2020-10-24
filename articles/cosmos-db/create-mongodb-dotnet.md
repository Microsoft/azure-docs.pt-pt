---
title: Construa uma aplicação web utilizando a API da Azure Cosmos DB para a MongoDB e .NET SDK
description: Apresenta uma amostra de código .NET que pode utilizar para ligar e consultar utilizando a API da Azure Cosmos DB para o MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 0cb5091b9b7d36c70454a65213113d34b29d76c2
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478954"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Construa uma aplicação web .NET utilizando a API da Azure Cosmos para a MongoDB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB é a base de dados NoSQL rápida da Microsoft com APIs abertos para qualquer escala. Pode criar e consultar rapidamente documentos, bases de dados chave/valor e gráficos, que beneficiam da distribuição global e das capacidades de escala horizontal no núcleo da Cosmos DB. 

Este quickstart demonstra como criar uma conta Cosmos com [a API da Azure Cosmos para a MongoDB.](mongodb-introduction.md) Em seguida, você construirá e implementará uma aplicação web de lista de tarefas construída usando o [controlador MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Pré-requisitos para executar a aplicação de exemplo

Para executar a amostra, você precisará de [Visual Studio](https://www.visualstudio.com/downloads/) e uma conta DB Azure Cosmos válida.

Se ainda não tem Visual Studio, baixe [a Edição Comunitária do Visual Studio 2019](https://www.visualstudio.com/downloads/) com a ASP.NET e a carga de trabalho de **desenvolvimento web** instalada com configuração.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

O exemplo descrito neste artigo é compatível com MongoDB.Driver versão 2.6.1.

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Primeiro, descarregue a aplicação de amostras do GitHub. 

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    mkdir "C:\git-samples"
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

Crie uma tarefa e insira-a na coleção

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

1. No [portal Azure](https://portal.azure.com/), na sua conta Cosmos, na navegação esquerda clique em **Connection String**e, em seguida, clique em **'Ler-escrever Teclas'** Vai utilizar os botões de copiar no lado direito do ecrã para copiar o Nome de Utilizador, a Palavra-passe e o Anfitrião para o ficheiro Dal.cs no próximo passo.

2. Abra o ficheiro **Dal.cs** no diretório **DAL**. 

3. Copie o valor de **nome de utilizador** a partir do portal (com o botão Copiar) e faça deste o valor de **nome de utilizador** no ficheiro **Dal.cs**. 

4. Em seguida, copie o valor de **anfitrião** do portal e faça do mesmo o valor de **anfitrião** no ficheiro **Dal.cs**. 

5. Por fim, copie o valor de **palavra-passe** do portal e faça do mesmo o valor de **palavra-passe** no ficheiro **Dal.cs**. 

Atualizou agora a sua aplicação com todas as informações que precisa para comunicar com a Cosmos DB. 
    
## <a name="run-the-web-app"></a>Executar a aplicação Web

1. No Visual Studio, clique com o botão direito do rato no projeto no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, escreva *MongoDB.Driver*.

3. A partir dos resultados, instale a biblioteca **MongoDB.Driver**. Esta ação instala o pacote MongoDB.Driver, bem como todas as dependências do mesmo.

4. Clique em CTRL + F5 para executar a aplicação. A aplicação é apresentada no browser. 

5. Clique em **Criar** no browser e crie algumas tarefas novas na aplicação de lista de tarefas.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Cosmos, criar uma coleção e executar uma aplicação de consola. Pode agora importar dados adicionais para a sua base de dados Cosmos. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json) (Importar dados do MongoDB para o Azure Cosmos DB)