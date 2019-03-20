---
title: Criar uma aplicação web com a API do Azure Cosmos DB para MongoDB e o SDK de .NET
description: Apresenta um exemplo de código .NET, que pode utilizar para ligar e consultar com a API do Azure Cosmos DB do MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: cc910359b5519d2cf1bd8a8bf019924dff660362
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852292"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Início rápido: Criar uma aplicação de web do .NET com a API do Azure Cosmos DB do MongoDB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar documentos, chave/valor e gráficos bases de dados, que beneficiam de capacidades de escalamento horizontal no núcleo do Cosmos DB e distribuição global rapidamente. 

Este início rápido demonstra como criar uma conta do Cosmos com [API do Azure Cosmos DB para o MongoDB](mongodb-introduction.md). Em seguida, vai criar e implementar uma aplicação de web de lista de tarefas criada com o [controlador .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Pré-requisitos para executar a aplicação de exemplo

Para executar o exemplo, precisará [Visual Studio](https://www.visualstudio.com/downloads/) e uma conta válida do Azure Cosmos DB.

Se ainda não tem o Visual Studio, transfira o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) com a carga de trabalho do **ASP.NET e desenvolvimento Web** instalada com a configuração.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

O exemplo descrito neste artigo é compatível com MongoDB.Driver versão 2.6.1.

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Em primeiro lugar, transfira a aplicação de exemplo do GitHub. 

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

Criar uma tarefa e inseri-lo na coleção

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

1. Na [portal do Azure](https://portal.azure.com/), na sua conta do Cosmos, no painel de navegação esquerdo clique **cadeia de ligação**e, em seguida, clique em **chaves de leitura-escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o Nome de Utilizador, a Palavra-passe e o Anfitrião para o ficheiro Dal.cs no próximo passo.

2. Abra o ficheiro **Dal.cs** no diretório **DAL**. 

3. Copie o valor de **nome de utilizador** a partir do portal (com o botão Copiar) e faça deste o valor de **nome de utilizador** no ficheiro **Dal.cs**. 

4. Em seguida, copie o valor de **anfitrião** do portal e faça do mesmo o valor de **anfitrião** no ficheiro **Dal.cs**. 

5. Por fim, copie o valor de **palavra-passe** do portal e faça do mesmo o valor de **palavra-passe** no ficheiro **Dal.cs**. 

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Cosmos DB. 
    
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

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como criar uma conta do Cosmos, criar uma coleção e executar uma aplicação de consola. Agora, pode importar dados adicionais à sua base de dados do Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)
