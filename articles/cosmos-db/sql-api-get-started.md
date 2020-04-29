---
title: 'Tutorial: Construa uma app de consola .NET para gerir dados na conta API Da Azure Cosmos DB SQL'
description: 'Tutorial: Aprenda a criar recursos API Azure Cosmos DB SQL utilizando uma aplicação de consola C#.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.openlocfilehash: 2681b2199f321f695bc621ed5580319a5e907b34
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78274016"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tutorial: Construa uma app de consola .NET para gerir dados na conta API Da Azure Cosmos DB SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java assíncrono](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Bem-vindo ao Azure Cosmos DB SQL API começar tutorial. Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do Cosmos DB.

Este tutorial utiliza a versão 3.0 ou posterior do [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Pode trabalhar com [.NET Framework ou .NET Core](https://dotnet.microsoft.com/download).

Este tutorial aborda:

> [!div class="checklist"]
>
> * Criação e ligação a uma conta Azure Cosmos
> * Configurar o seu projeto no Estúdio Visual
> * Criação de uma base de dados e um recipiente
> * Adicionar itens ao contentor
> * Consulta o contentor
> * Executar operações de criação, leitura, atualização e exclusão (CRUD) no item
> * Eliminar a base de dados

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Salte para a [secção de solução tutorial completa](#GetSolution) para obter instruções rápidas.

Agora comecemos!

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se já tem uma conta que deseja utilizar, ignore esta secção. Para utilizar o Emulador DB Azure Cosmos, siga os passos do [Emulador DB da Azure Cosmos](local-emulator.md) para configurar o emulador. Em seguida, salte para a frente para o [Passo 2: Configurar o seu projeto Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Passo 2: Configurar o seu projeto De Estúdio Visual

1. Open Visual Studio e selecione **Criar um novo projeto.**
1. Em **Criar um novo projeto,** escolha a App consola **(.NET Framework)** para C#e, em seguida, selecione **Next**.
1. Nomeie o seu projeto *CosmosGettingStartedTutorial,* e depois selecione **Criar**.

    ![Configure o seu projeto](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. No **Solution Explorer,** clique à direita na sua nova aplicação de consola, que está sob a sua solução Visual Studio, e selecione **Manage NuGet Packages**.
1. No **NuGet Package Manager,** **selecione Browse** e procure *microsoft.Azure.Cosmos*. Escolha **Microsoft.Azure.Cosmos** e selecione **Instalar**.

   ![Instale nuGet para Azure Cosmos DB Client SDK](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Ótimo! Agora que concluímos a configuração, comecemos a escrever certos códigos. Para o projeto concluído deste tutorial, consulte [desenvolver uma aplicação de consola .NET utilizando o Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Passo 3: Ligar a uma conta do Azure Cosmos DB

1. Substitua as referências no início da sua aplicação C# no ficheiro *Program.cs* por estas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Adicione estas constantes e `Program` variáveis na sua classe.

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
   > Se estiver familiarizado com a versão anterior do .NET SDK, poderá estar familiarizado com a *recolha* e *o documento*de termos. Uma vez que o Azure Cosmos DB suporta vários modelos API, a versão 3.0 do .NET SDK utiliza o recipiente e *o item*de termos *genéricos.* Um *recipiente* pode ser uma coleção, gráfico ou mesa. Um *item* pode ser um documento, borda/vértice, ou linha, e é o conteúdo dentro de um recipiente. Para mais informações, consulte Trabalhar com bases de [dados, contentores e itens em Azure Cosmos DB](databases-containers-items.md).

1. Abra o [portal Azure.](https://portal.azure.com) Encontre a sua conta Azure Cosmos DB e, em seguida, selecione **Keys**.

   ![Obtenha chaves DB Azure Cosmos do portal Azure](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. Em *Program.cs,* substitua-o `<your endpoint URL>` pelo valor de **URI**. Substitua `<your primary key>` pelo valor da **CHAVE PRIMÁRIA**.

1. Abaixo do método **Principal,** adicione uma nova tarefa assíncrona chamada **GetStartedDemoAsync,** que instantaneamente o nosso novo `CosmosClient`.

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

    Utilizamos o **GetStartedDemoAsync** como ponto de entrada que chama métodos que operam nos recursos DB da Azure Cosmos.

1. Adicione o seguinte código para executar a tarefa **assíncrona GetStartedDemoAsync** a partir do seu método **Principal.** O método **Principal** captura as exceções e escreve-as na consola.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecione F5 para executar a sua aplicação.

    A consola exibe a mensagem: **Fim da demonstração, prima qualquer tecla para sair.** Esta mensagem confirma que a sua aplicação fez uma ligação com o Azure Cosmos DB. Em seguida, pode fechar a janela da consola.

Parabéns! Ligaste-te com sucesso a uma conta da Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados

As bases de dados são os contentores lógicos dos itens particionados em contentores. Ou `CreateDatabaseIfNotExistsAsync` o `CreateDatabaseAsync` método ou o método da classe [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) podem criar uma base de dados.

1. Copie e `CreateDatabaseAsync` cole o `GetStartedDemoAsync` método abaixo do seu método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`cria uma nova `FamilyDatabase` base de dados com ID se já não `databaseId` existir, que tem o ID especificado a partir do campo.

1. Copie e cole o código abaixo onde instantaneamente o CosmosClient para chamar o método **CreateDatabaseAsync** que acabou de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    O seu *Program.cs* deve agora ser assim, com o seu ponto final e chave primária preenchido.

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

1. Selecione F5 para executar a sua aplicação.

   > [!NOTE]
   > Se obtém um erro de "exceção indisponível do serviço 503", é possível que as [portas necessárias](performance-tips.md#networking) para o modo de conectividade direta sejam bloqueadas por uma firewall. Para corrigir este problema, abra as portas necessárias ou utilize a conectividade do modo gateway, como mostrado no seguinte código:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Parabéns! Criaste com sucesso uma base de dados da Azure Cosmos.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Passo 5: Criar um recipiente

> [!WARNING]
> O `CreateContainerIfNotExistsAsync` método cria um novo recipiente, que tem implicações nos preços. Para mais informações, visite a nossa [página de preços.](https://azure.microsoft.com/pricing/details/cosmos-db/)
>
>

Um recipiente pode ser criado utilizando o método [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) `CosmosDatabase` ou [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) na classe. Um recipiente é composto por itens (documentos JSON se SQL API) e lógica de aplicação do lado do servidor associado no JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo utilizador e gatilhos.

1. Copie e `CreateContainerAsync` cole o `CreateDatabaseAsync` método abaixo do seu método. `CreateContainerAsync`cria um novo recipiente `FamilyContainer` com o ID se já não existir, `containerId` utilizando o `LastName` ID especificado a partir do campo dividido por propriedade.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copie e cole o código abaixo onde instantaneamente o CosmosClient para chamar o método **CreateContainer** que acabou de adicionar.

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

1. Selecione F5 para executar a sua aplicação.

Parabéns! Criaste com sucesso um contentor Azure Cosmos.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Passo 6: Adicionar itens ao recipiente

O método [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) da `CosmosContainer` classe pode criar um item. Ao utilizar a API SQL, os itens são projetados como documentos, que são conteúdo jSON arbitrário definido pelo utilizador. Agora pode inserir um item no seu recipiente Azure Cosmos.

Primeiro, vamos criar `Family` uma classe que represente objetos armazenados dentro do Azure Cosmos DB nesta amostra. Também criaremos `Parent` `Child` `Pet` `Address` subclasses que são usadas `Family`no interior. O artigo deve `Id` ter uma `id` propriedade serializada como em JSON.

1. Selecione Ctrl+Shift+A para abrir **Adicionar Novo Item**. Adicione uma `Family.cs` nova classe ao seu projeto.

    ![Screenshot de adicionar uma nova classe Family.cs no projeto](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Copiar e colar `Family` `Parent`o, `Pet`, `Address` `Family.cs` `Child`e classe em .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. De volta *ao Program.cs,* adicione o método após o `AddItemsToContainerAsync` seu `CreateContainerAsync` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    O código verifica para ter certeza de que um item com a mesma identificação já não existe. Vamos inserir dois itens, um para a *Família Andersen* e a *Família Wakefield.*

1. Adicione uma `AddItemsToContainerAsync` chamada `GetStartedDemoAsync` no método.

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

1. Selecione F5 para executar a sua aplicação.

Parabéns! Criaste com sucesso dois itens Azure Cosmos.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Passo 7: Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB suporta consultas extensas em documentos JSON armazenados em cada contentor. Para mais informações, consulte [Começar com consultas SQL](sql-api-sql-query.md). O seguinte código de amostra mostra como executar uma consulta contra os itens que inserimos no passo anterior.

1. Copie e `QueryItemsAsync` cole o `AddItemsToContainerAsync` método após o seu método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adicione uma ``QueryItemsAsync`` chamada ``GetStartedDemoAsync`` no método.

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

1. Selecione F5 para executar a sua aplicação.

Parabéns! Conseguiu um contentor Azure Cosmos.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Passo 8: Substitua um item JSON

Vamos atualizar um artigo em Azure Cosmos DB. Vamos mudar a `IsRegistered` propriedade `Family` de `Grade` e de uma das crianças.

1. Copie e `ReplaceFamilyItemAsync` cole o `QueryItemsAsync` método após o seu método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adicione uma `ReplaceFamilyItemAsync` chamada `GetStartedDemoAsync` no método.

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

1. Selecione F5 para executar a sua aplicação.

Parabéns! Substituiste com sucesso um artigo da Azure Cosmos.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Passo 9: Apagar o item

Vamos apagar um artigo em Azure Cosmos DB.

1. Copie e `DeleteFamilyItemAsync` cole o `ReplaceFamilyItemAsync` método após o seu método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adicione uma `DeleteFamilyItemAsync` chamada `GetStartedDemoAsync` no método.

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

1. Selecione F5 para executar a sua aplicação.

Parabéns! Apagaste com sucesso um item da Azure Cosmos.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados

Agora vamos apagar a nossa base de dados. A eliminação da base de dados criada remove a base de dados e todos os recursos para crianças. Os recursos incluem contentores, itens e quaisquer procedimentos armazenados, funções definidas pelo utilizador e gatilhos. Também nos livramos da `CosmosClient` ocorrência.

1. Copie e `DeleteDatabaseAndCleanupAsync` cole o `DeleteFamilyItemAsync` método após o seu método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adicione uma ``DeleteDatabaseAndCleanupAsync`` chamada ``GetStartedDemoAsync`` no método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Selecione F5 para executar a sua aplicação.

Parabéns! Apagou com sucesso uma base de dados da Azure Cosmos.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Passo 11: Executar a sua aplicação de consola C# em conjunto!

Selecione F5 no Estúdio Visual para construir e executar a aplicação em modo dedebug.

Deve ver a saída de toda a sua aplicação numa janela de consola. A saída mostra os resultados das consultas que adicionámos. Deve coincidir com o texto de exemplo abaixo.

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

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a>Obter a solução completa do tutorial

Se não teve tempo de completar os passos deste tutorial, ou apenas deseja ndo o download das amostras de código, pode descarregá-la.

Para construir `GetStarted` a solução, precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta do Azure Cosmos DB][cosmos-db-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) está disponível no GitHub.

Para restaurar as referências ao Azure Cosmos DB .NET SDK no Estúdio Visual, clique na solução no **Solution Explorer**, e, em seguida, selecione **Restaurar pacotes NuGet**. Em seguida, no ficheiro *App.config,* atualize os `EndPointUri` valores e valores `PrimaryKey` descritos no Passo [3: Ligue-se a uma conta Azure Cosmos DB](#Connect).

É isso, constrói-o, e estás a caminho!

## <a name="next-steps"></a>Passos seguintes

* Quer um tutorial ASP.NET MVC mais complexo? Ver [Tutorial: Desenvolver uma aplicação web ASP.NET Core MVC com o Azure Cosmos DB utilizando .NET SDK](sql-api-dotnet-application.md).
* Quer fazer testes de escala e desempenho com o Azure Cosmos DB? Ver [Performance e testes de escala com Azure Cosmos DB](performance-testing.md).
* Para aprender a monitorizar os pedidos, uso e armazenamento da Azure Cosmos DB, consulte as métricas de [desempenho e armazenamento do Monitor em Azure Cosmos DB](monitor-accounts.md).
* Para fazer consultas contra o nosso conjunto de dados de amostras, consulte o [Parque De Recreio](https://www.documentdb.com/sql/demo)da Consulta .
* Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
