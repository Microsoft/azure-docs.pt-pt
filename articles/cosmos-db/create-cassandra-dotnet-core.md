---
title: 'Quickstart: Cassandra API com .NET Core - Azure Cosmos DB'
description: Este quickstart mostra como usar a API API AZURE Cosmos DB Cassandra para criar uma aplicação de perfil com o portal Azure e .NET Core
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f228386d8cf0e708080b7f6c5f6cef7258b2eafb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099985"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>Quickstart: Construa uma app Cassandra com .NET Core e Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Este quickstart mostra como usar .NET Core e a [API](cassandra-introduction.md) API AZure Cosmos DB Cassandra para construir uma aplicação de perfil clonando um exemplo do GitHub. Este início rápido também mostra como pode utilizar o portal do Azure baseado na Web para criar uma conta do Azure Cosmos DB.

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a tabela, a chave/valor e as bases de dados de gráficos que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Em alternativa, pode [experimentar gratuitamente a Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos.

Além disso, necessita: 
* Se ainda não tiver o Visual Studio 2019 instalado,  pode baixar e utilizar a [Edição Comunitária visual 2019 gratuita do Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Instalar [Git](https://www.git-scm.com/) para clonar o exemplo.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação do Cassandra API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma linha de comandos. Crie uma nova pasta com o nome `git-samples`. Em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. Em seguida, abra o ficheiro da solução CassandraQuickStartSample no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber de que forma o código cria os recursos da base de dados, pode rever os fragmentos seguintes. Os cortes são todos retirados do ficheiro dentro do `Program.cs` `async Task ProcessAsync()` método, instalados na `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart` pasta. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string).

* Inicie a sessão através da ligação a um ponto final de cluster do Cassandra. O Cassandra API no Azure Cosmos DB suporta apenas TLSv1.2. 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* Largue o espaço-chave existente se já existir.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* Criar um keyspace novo.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* Crie uma nova tabela.

   ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
   ```

* Insira entidades de utilizador utilizando o objeto de IMapper com uma nova sessão que liga ao keyspace do perfil.

    ```csharp
    await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
    ```

* Efetue uma consulta para obter as informações de todos os utilizadores.

    ```csharp
    foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
    {
        Console.WriteLine(user);
    }
    ```

* Efetue uma consulta para obter as informações de um único utilizador.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. As informações da cadeia de ligação permitem à aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](https://portal.azure.com/), clique em **Cadeia de Ligação**.

1. Utilize o :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: botão no lado direito do ecrã para copiar o valor USERNAME.

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Cadeia de ligação":::

1. No Estúdio Visual, abra o ficheiro .cs Programa. 

1. Cole o valor do NOME DE UTILIZADOR do portal em `<PROVIDE>` na linha 13.

    A Linha 13 do Program.cs deve agora ter um aspeto semelhante a 

    `private const string UserName = "cosmos-db-quickstart";`

    Também pode colar o mesmo valor sobre a `<PROVIDE>` linha 15 para o valor PONTO DE CONTACTO:

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. Volte ao portal e copie o valor do NOME DE UTILIZADOR. Cole o valor da PALAVRA-PASSE do portal em `<PROVIDE>` na linha 14.

    A Linha 14 do Program.cs deve agora ter um aspeto semelhante a 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. Volte ao portal e copie o valor do PONTO DE CONTACTO. Cole o valor PONTO DE CONTACTO do portal sobre a `<PROVIDE>` linha 16.

    Linha 16 do Programa.cs deve agora parecer semelhante a 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. Guarde o ficheiro Program.cs.
    
## <a name="run-the-net-core-app"></a>Executar a aplicação .NET Core

1. No Estúdio Visual, selecione **Tools**  >  **NuGet Package Manager** Package Manager  >  **Consola**.

2. Na linha de comandos, utilize o comando seguinte para instalar o pacote NuGet do controlador do .NET. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Prima CTRL + F5 para executar a aplicação. A aplicação é apresentada na janela da consola. 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="Ver e verificar a saída":::

    Prima CTRL + C para interromper a execução do programa e feche a janela da consola. 
    
4. No portal do Azure, abra o **Data Explorer** para consultar, modificar e trabalhar com estes dados novos.

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="Ver os dados no Data Explorer":::

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma coleção com o Data Explorer e a executar uma aplicação Web. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)
