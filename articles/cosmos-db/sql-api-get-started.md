---
title: 'Tutorial: compilar um aplicativo de console .NET para gerenciar dados em Azure Cosmos DB conta da API do SQL'
description: 'Tutorial: saiba como criar Azure Cosmos DB recursos da API do SQL usando C# um aplicativo de console.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.openlocfilehash: a8af36da7b9043492f1ed3c77dcc1b35dc2936fe
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132563"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tutorial: compilar um aplicativo de console .NET para gerenciar dados em Azure Cosmos DB conta da API do SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Bem-vindo ao tutorial de introdução à API do SQL Azure Cosmos DB. Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do Cosmos DB.

Este tutorial usa a versão 3,0 ou posterior do [SDK do .net Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Você pode trabalhar com o [.NET Framework ou o .NET Core](https://dotnet.microsoft.com/download).

Este tutorial aborda:

> [!div class="checklist"]
>
> * Criar e ligar a uma conta do Cosmos do Azure
> * Configurando seu projeto no Visual Studio
> * Criando um banco de dados e um contêiner
> * Adicionar itens ao contentor
> * Consulta o contentor
> * Executando operações CRUD (criar, ler, atualizar e excluir) no item
> * Eliminar a base de dados

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Vá para a [seção obter a solução completa do tutorial](#GetSolution) para obter instruções rápidas.

Agora comecemos!

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se você já tiver uma conta que deseja usar, pule esta seção. Para usar o emulador de Azure Cosmos DB, siga as etapas em [emulador de Azure Cosmos DB](local-emulator.md) para configurar o emulador. Em seguida, pule para a [etapa 2: configurar seu projeto do Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Etapa 2: configurar seu projeto do Visual Studio

1. Abra o Visual Studio e selecione **criar um novo projeto**.
1. Em **criar um novo projeto**, escolha **aplicativo de console (.NET Framework)** para C#e, em seguida, selecione **Avançar**.
1. Nomeie o projeto *CosmosGettingStartedTutorial*e, em seguida, selecione **criar**.

    ![Configurar seu projeto](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. No **Gerenciador de soluções**, clique com o botão direito do mouse no novo aplicativo de console, que está em sua solução do Visual Studio e selecione **gerenciar pacotes NuGet**.
1. No **Gerenciador de pacotes NuGet**, selecione **procurar** e procure *Microsoft. Azure. Cosmos*. Escolha **Microsoft. Azure. Cosmos** e selecione **instalar**.

   ![Instalar o NuGet para Azure Cosmos DB SDK do cliente](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Ótimo! Agora que concluímos a configuração, comecemos a escrever certos códigos. Para o projeto concluído deste tutorial, consulte [desenvolvendo um aplicativo de console .NET usando Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Passo 3: Ligar a uma conta do Azure Cosmos DB

1. Substitua as referências no início do C# aplicativo no arquivo *Program.cs* com estas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Adicione essas constantes e variáveis à sua classe `Program`.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Se você estiver familiarizado com a versão anterior do SDK do .NET, talvez esteja familiarizado com a *coleção* de termos e o *documento*. Como Azure Cosmos DB dá suporte a vários modelos de API, a versão 3,0 do SDK do .NET usa o *contêiner* e o *Item*de termos genéricos. Um *contêiner* pode ser uma coleção, um gráfico ou uma tabela. Um *Item* pode ser um documento, borda/vértice ou linha, e é o conteúdo dentro de um contêiner. Para obter mais informações, consulte [trabalhar com bancos de dados, contêineres e itens em Azure Cosmos DB](databases-containers-items.md).

1. Abra o [Portal do Azure](https://portal.azure.com). Localize sua conta do Azure Cosmos DB e, em seguida, selecione **chaves**.

   ![Obter Azure Cosmos DB chaves de portal do Azure](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. Em *Program.cs*, substitua `<your endpoint URL>` pelo valor de **URI**. Substitua `<your primary key>` pelo valor da **chave primária**.

1. Abaixo do método **Main** , adicione uma nova tarefa assíncrona chamada **GetStartedDemoAsync**, que instancia nosso novo `CosmosClient`.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    Usamos **GetStartedDemoAsync** como o ponto de entrada que chama métodos que operam em Azure Cosmos DB recursos.

1. Adicione o código a seguir para executar a tarefa assíncrona **GetStartedDemoAsync** do seu método **principal** . O método **Principal** captura as exceções e escreve-as na consola.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecione F5 para executar o aplicativo.

    O console exibe a mensagem: **fim da demonstração, pressione qualquer tecla para sair.** Essa mensagem confirma que seu aplicativo fez uma conexão com Azure Cosmos DB. Em seguida, pode fechar a janela da consola.

Parabéns! Você se conectou com êxito a uma conta de Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados

As bases de dados são os contentores lógicos dos itens particionados em contentores. O método `CreateDatabaseIfNotExistsAsync` ou `CreateDatabaseAsync` da classe [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) pode criar um banco de dados.

1. Copie e cole o método `CreateDatabaseAsync` abaixo do método `GetStartedDemoAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` cria um novo banco de dados com a ID `FamilyDatabase` se ele ainda não existir, que tem a ID especificada no campo `databaseId`.

1. Copie e cole o código abaixo, onde você instancia o CosmosClient para chamar o método **CreateDatabaseAsync** que você acabou de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    O *Program.cs* agora deve ter esta aparência, com o ponto de extremidade e a chave primária preenchida.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Selecione F5 para executar o aplicativo.

   > [!NOTE]
   > Se você receber uma "exceção do serviço 503 indisponível", é possível que as [portas](performance-tips.md#networking) necessárias para o modo direto sejam bloqueadas por um firewall. Para corrigir esse problema, abra as [portas](performance-tips.md#networking) necessárias ou tente usar o modo de gateway, conforme mostrado abaixo.
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Parabéns! Você criou um banco de dados Cosmos do Azure com êxito.  

## <a id="CreateColl"></a>Etapa 5: criar um contêiner

> [!WARNING]
> O método `CreateContainerIfNotExistsAsync` cria um novo contêiner, que tem implicações de preço. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Um contêiner pode ser criado usando o método [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) ou [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) na classe `CosmosDatabase`. Um contêiner consiste em itens (documentos JSON se a API do SQL) e lógica de aplicativo associada no lado do servidor em JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo usuário e gatilhos.

1. Copie e cole o método `CreateContainerAsync` abaixo do método `CreateDatabaseAsync`. `CreateContainerAsync` cria um novo contêiner com a ID `FamilyContainer` se ele ainda não existir, usando a ID especificada do campo `containerId` particionado pela propriedade `LastName`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copie e cole o código abaixo, onde você instanciou o CosmosClient para chamar o método **CreateContainer** que você acabou de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Selecione F5 para executar o aplicativo.

Parabéns! Você criou um contêiner Cosmos do Azure com êxito.  

## <a id="CreateDoc"></a>Etapa 6: adicionar itens ao contêiner

O método [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) da classe `CosmosContainer` pode criar um item. Ao usar a API do SQL, os itens são projetados como documentos, que são conteúdo JSON arbitrário definido pelo usuário. Agora você pode inserir um item em seu contêiner Cosmos do Azure.

Primeiro, vamos criar uma classe `Family` que representa os objetos armazenados em Azure Cosmos DB neste exemplo. Também criaremos `Parent`, `Child`, `Pet``Address` subclasses que são usadas no `Family`. O item deve ter uma propriedade `Id` serializada como `id` em JSON.

1. Selecione CTRL + SHIFT + A para abrir **Adicionar novo item**. Adicione uma nova classe `Family.cs` ao seu projeto.

    ![Captura de tela da adição de uma nova classe Family.cs no projeto](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Copie e cole a `Family`, `Parent`, `Child`, `Pet`e `Address` classe em `Family.cs`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. De volta ao *Program.cs*, adicione o método `AddItemsToContainerAsync` após o método `CreateContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

    O código verifica se há um item com a mesma ID que ainda não existe. Vamos inserir dois itens, um para a *família Andersen* e a *família Barros*.

1. Adicione uma chamada para `AddItemsToContainerAsync` no método `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Selecione F5 para executar o aplicativo.

Parabéns! Você criou com êxito dois itens do Azure Cosmos.  

## <a id="Query"></a>Passo 7: Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB dá suporte a consultas avançadas em documentos JSON armazenados em cada contêiner. Para obter mais informações, consulte [introdução às consultas SQL](sql-api-sql-query.md). O código de exemplo a seguir mostra como executar uma consulta em relação aos itens que inserimos na etapa anterior.

1. Copie e cole o método `QueryItemsAsync` após o método `AddItemsToContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adicione uma chamada para ``QueryItemsAsync`` no método ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Selecione F5 para executar o aplicativo.

Parabéns! Você consultou com êxito um contêiner Cosmos do Azure.

## <a id="ReplaceItem"></a>Etapa 8: substituir um item JSON

Agora, atualizaremos um item em Azure Cosmos DB. Alteraremos a propriedade `IsRegistered` da `Family` e a `Grade` de um dos filhos.

1. Copie e cole o método `ReplaceFamilyItemAsync` após o método `QueryItemsAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adicione uma chamada para `ReplaceFamilyItemAsync` no método `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Selecione F5 para executar o aplicativo.

Parabéns! Você substituiu um item Cosmos do Azure com êxito.

## <a id="DeleteDocument"></a>Etapa 9: Excluir item

Agora, vamos excluir um item em Azure Cosmos DB.

1. Copie e cole o método `DeleteFamilyItemAsync` após o método `ReplaceFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adicione uma chamada para `DeleteFamilyItemAsync` no método `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Selecione F5 para executar o aplicativo.

Parabéns! Você excluiu com êxito um item Cosmos do Azure.

## <a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados

Agora, vamos excluir nosso banco de dados. Excluir o banco de dados criado remove o banco de dados e todos os recursos filhos. Os recursos incluem contêineres, itens e quaisquer procedimentos armazenados, funções definidas pelo usuário e gatilhos. Também descartamos a instância de `CosmosClient`.

1. Copie e cole o método `DeleteDatabaseAndCleanupAsync` após o método `DeleteFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adicione uma chamada para ``DeleteDatabaseAndCleanupAsync`` no método ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Selecione F5 para executar o aplicativo.

Parabéns! Você excluiu com êxito um banco de dados Cosmos do Azure.

## <a id="Run"></a>Passo 11: Executar a sua aplicação de consola C# em conjunto!

Selecione F5 no Visual Studio para compilar e executar o aplicativo no modo de depuração.

Você deve ver a saída de todo o seu aplicativo em uma janela de console. A saída mostra os resultados das consultas que adicionamos. Ele deve corresponder ao texto de exemplo abaixo.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Parabéns! Concluiu este tutorial e a sua aplicação de consola C# está a funcionar!

## <a id="GetSolution"></a>Obter a solução completa do tutorial

Se você não tiver tempo para concluir as etapas neste tutorial ou apenas quiser baixar os exemplos de código, poderá baixá-lo.

Para criar a solução de `GetStarted`, você precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta de Azure Cosmos DB][cosmos-db-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) está disponível no GitHub.

Para restaurar as referências para o SDK do .NET Azure Cosmos DB no Visual Studio, clique com o botão direito do mouse na solução em **Gerenciador de soluções**e, em seguida, selecione **restaurar pacotes NuGet**. Em seguida, no arquivo *app. config* , atualize os valores de `EndPointUri` e `PrimaryKey` conforme descrito na [etapa 3: conectar-se a uma conta de Azure Cosmos DB](#Connect).

É isso, crie e você está no caminho!

## <a name="next-steps"></a>Passos seguintes

* Quer um tutorial ASP.NET MVC mais complexo? Consulte [tutorial: desenvolver um aplicativo web ASP.NET Core MVC com Azure Cosmos DB usando o SDK do .net](sql-api-dotnet-application.md).
* Deseja fazer testes de escala e desempenho com o Azure Cosmos DB? Consulte [teste de desempenho e escala com Azure Cosmos DB](performance-testing.md).
* Para saber como monitorar Azure Cosmos DB solicitações, o uso e o armazenamento, consulte [monitorar as métricas de desempenho e armazenamento no Azure Cosmos DB](monitor-accounts.md).
* Para executar consultas em nosso conjunto de exemplos de exemplo, consulte a [playground para consultas](https://www.documentdb.com/sql/demo).
* Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
