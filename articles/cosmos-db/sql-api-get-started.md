---
title: 'Tutorial: Construa uma aplicação de consola .NET para gerir dados na conta AZure Cosmos DB SQL API'
description: 'Tutorial: Saiba como criar recursos Azure Cosmos DB SQL API utilizando uma aplicação de consola C#.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 587fe536e860f3039bfd3a2d2c1e3c76cb40e4d5
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278462"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tutorial: Construa uma aplicação de consola .NET para gerir dados na conta AZure Cosmos DB SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java assíncrono](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Bem-vindos ao Azure Cosmos DB SQL API começam a tutorial. Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do Cosmos DB.

Este tutorial utiliza a versão 3.0 ou mais tarde do [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Pode trabalhar com [.NET Framework ou .NET Core](https://dotnet.microsoft.com/download).

Este tutorial aborda:

> [!div class="checklist"]
>
> * Criar e conectar-se a uma conta Azure Cosmos
> * Configurar o seu projeto no Visual Studio
> * Criação de uma base de dados e um contentor
> * Adicionar itens ao contentor
> * Consulta o contentor
> * Realizar operações de criação, leitura, atualização e eliminação (CRUD) no item
> * Eliminar a base de dados

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Salte para a [secção Obter a solução tutorial completa](#GetSolution) para obter instruções rápidas.

Agora comecemos!

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se já tem uma conta que pretende utilizar, ignore esta secção. Para utilizar o Emulador Azure Cosmos DB, siga os passos no [Azure Cosmos DB Emulator](local-emulator.md) para configurar o emulador. Em seguida, avance para o [Passo 2: Crie o seu projeto Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Passo 2: Crie o seu projeto Visual Studio

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**
1. In **Create a new project**, escolha App consola **(.NET Framework)** para C#e, em seguida, selecione **Next**.
1. Nomeie o seu projeto *CosmosGettingStartedTutorial*e, em seguida, **selecione Create**.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="Configure o seu projeto":::

1. No **Solution Explorer,** clique com o botão direito na sua nova aplicação de consola, que está sob a sua solução Visual Studio, e selecione **Gerir Pacotes NuGet**.
1. No Gestor de **Pacotes NuGet,** selecione **Procurar** *microsoft.Azure.Cosmos*. Escolha **Microsoft.Azure.Cosmos** e selecione **Instalar**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Configure o seu projeto":::

   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Excelente! Agora que concluímos a configuração, comecemos a escrever certos códigos. Para o projeto concluído deste tutorial, consulte desenvolver uma aplicação de [consola .NET utilizando a Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Passo 3: Ligar a uma conta do Azure Cosmos DB

1. Substitua as referências no início da sua aplicação C# no ficheiro *Program.cs* com estas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Adicione estas constantes e variáveis na sua `Program` classe.

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
   > Se estiver familiarizado com a versão anterior do .NET SDK, poderá estar familiarizado com a *recolha* e *documento*dos termos. Uma vez que a Azure Cosmos DB suporta vários modelos API, a versão 3.0 do .NET SDK utiliza o *recipiente* e *o item*genéricos . Um *recipiente* pode ser uma coleção, gráfico ou mesa. Um *item* pode ser um documento, borda/vértice, ou linha, e é o conteúdo dentro de um recipiente. Para obter mais informações, consulte [Trabalhar com bases de dados, contentores e itens em Azure Cosmos DB](account-databases-containers-items.md).

1. Abra o [portal do Azure](https://portal.azure.com). Encontre a sua conta DB Azure Cosmos e, em seguida, selecione **Keys**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Configure o seu projeto":::

1. Em *Program.cs,* `<your endpoint URL>` substitua-se pelo valor da **URI**. `<your primary key>`Substitua-o pelo valor da **CHAVE PRIMÁRIA**.

1. Abaixo do método **Principal,** adicione uma nova tarefa assíncronea chamada **GetStartedDemoAsync,** que instantaneamente o nosso novo `CosmosClient` .

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

    Utilizamos **o GetStartedDemoAsync** como o ponto de entrada que chama métodos que operam nos recursos DB da Azure Cosmos.

1. Adicione o seguinte código para executar a tarefa assíncron **GetStartedDemoAsync** do seu método **Principal.** O método **Principal** captura as exceções e escreve-as na consola.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecione F5 para executar a sua aplicação.

    A consola exibe a mensagem: **Fim da demonstração, prima qualquer tecla para sair.** Esta mensagem confirma que a sua aplicação fez uma ligação ao Azure Cosmos DB. Em seguida, pode fechar a janela da consola.

Parabéns! Ligaste-te com sucesso a uma conta DB da Azure Cosmos.

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados

As bases de dados são os contentores lógicos dos itens particionados em contentores. Ou o `CreateDatabaseIfNotExistsAsync` método ou método da classe `CreateDatabaseAsync` [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) pode criar uma base de dados.

1. Copie e cole o `CreateDatabaseAsync` método abaixo do seu `GetStartedDemoAsync` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` cria uma nova base de dados com ID `FamilyDatabase` se já não existir, que tem o ID especificado a partir do `databaseId` campo.

1. Copie e cole o código abaixo onde instantaneamente o CosmosClient para ligar para o método **CreateDatabaseAsync** que acaba de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    O seu *Program.cs* deve agora ser assim, com o seu ponto final e a chave primária preenchidas.

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
   > Se obtém um erro de "exceção indisponíveis de serviço 503", é possível que as [portas necessárias](sql-sdk-connection-modes.md#service-port-ranges) para o modo de conectividade direta sejam bloqueadas por uma firewall. Para corrigir este problema, abra as portas necessárias ou utilize a conectividade do modo gateway, como indicado no seguinte código:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Parabéns! Criou com sucesso uma base de dados da Azure Cosmos.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Passo 5: Criar um recipiente

> [!WARNING]
> O método `CreateContainerIfNotExistsAsync` cria um novo recipiente, que tem implicações nos preços. Para mais detalhes, visite a nossa [página de preços.](https://azure.microsoft.com/pricing/details/cosmos-db/)
>
>

Um recipiente pode ser criado utilizando o método [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) ou [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) na `CosmosDatabase` classe. Um recipiente é composto por itens (documentos JSON se SQL API) e lógica de aplicação associada do lado do servidor no JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo utilizador e gatilhos.

1. Copie e cole o `CreateContainerAsync` método abaixo do seu `CreateDatabaseAsync` método. `CreateContainerAsync`  cria um novo recipiente com o ID `FamilyContainer` se já não existir, utilizando o ID especificado a partir do `containerId` campo dividido por `LastName` propriedade.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copie e cole o código abaixo onde instantaneamente o CosmosClient para chamar o método **CreateContainer** que acaba de adicionar.

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

Parabéns! Criou com sucesso um contentor Azure Cosmos.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Passo 6: Adicione itens ao recipiente

O método [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) da `CosmosContainer` classe pode criar um item. Ao utilizar a API SQL, os itens são projetados como documentos, que são conteúdo arbitrário JSON definido pelo utilizador. Pode agora inserir um item no seu recipiente Azure Cosmos.

Primeiro, vamos criar uma `Family` classe que represente objetos armazenados dentro da Azure Cosmos DB nesta amostra. Também criaremos `Parent` `Child` `Pet` `Address` subclasses que são usadas dentro `Family` de . O item deve ter uma `Id` propriedade serializada como `id` em JSON.

1. Selecione Ctrl+Shift+A para abrir **Adicionar Novo Item**. Adicione uma nova classe `Family.cs` ao seu projeto.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Configure o seu projeto":::

1. Copiar e colar `Family` `Parent` o, `Child` , , e classe em `Pet` `Address` `Family.cs` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. De volta *Program.cs,* adicione o `AddItemsToContainerAsync` método após o seu `CreateContainerAsync` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    O código verifica se um item com a mesma identificação já não existe. Vamos inserir dois itens, um para a *Família Andersen* e para a *Família Wakefield.*

1. Adicione uma chamada para `AddItemsToContainerAsync` o `GetStartedDemoAsync` método.

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

Parabéns! Criou com sucesso dois itens da Azure Cosmos.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Passo 7: Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB suporta consultas extensas em documentos JSON armazenados em cada contentor. Para mais informações, consulte [Começar com consultas SQL.](sql-api-sql-query.md) O seguinte código de amostra mostra como executar uma consulta contra os itens que inserimos no passo anterior.

1. Copie e cole o `QueryItemsAsync` método após o seu `AddItemsToContainerAsync` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adicione uma chamada para ``QueryItemsAsync`` o ``GetStartedDemoAsync`` método.

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

Parabéns! Perguntaste com sucesso um contentor do Azure Cosmos.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Passo 8: Substituir um item JSON

Agora, vamos atualizar um artigo no Azure Cosmos DB. Mudaremos a `IsRegistered` propriedade de `Family` uma das `Grade` crianças.

1. Copie e cole o `ReplaceFamilyItemAsync` método após o seu `QueryItemsAsync` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adicione uma chamada para `ReplaceFamilyItemAsync` o `GetStartedDemoAsync` método.

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

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Passo 9: Eliminar item

Agora, vamos apagar um item em Azure Cosmos DB.

1. Copie e cole o `DeleteFamilyItemAsync` método após o seu `ReplaceFamilyItemAsync` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adicione uma chamada para `DeleteFamilyItemAsync` o `GetStartedDemoAsync` método.

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

Parabéns! Apagou com sucesso um artigo da Azure Cosmos.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados

Agora vamos apagar a nossa base de dados. A eliminação da base de dados criada remove a base de dados e todos os recursos para crianças. Os recursos incluem contentores, itens e quaisquer procedimentos armazenados, funções definidas pelo utilizador e gatilhos. Também dispomos do `CosmosClient` caso.

1. Copie e cole o `DeleteDatabaseAndCleanupAsync` método após o seu `DeleteFamilyItemAsync` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adicione uma chamada para ``DeleteDatabaseAndCleanupAsync`` o ``GetStartedDemoAsync`` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Selecione F5 para executar a sua aplicação.

Parabéns! Apagou com sucesso uma base de dados da Azure Cosmos.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Passo 11: Executar a sua aplicação de consola C# em conjunto!

Selecione F5 no Estúdio Visual para construir e executar a aplicação no modo depuramento.

Você deve ver a saída de toda a sua aplicação em uma janela de consola. A saída mostra os resultados das consultas que adicionámos. Deve corresponder ao texto de exemplo abaixo.

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

Se não teve tempo de completar os passos deste tutorial, ou apenas quiser descarregar as amostras de código, pode descarregá-lo.

Para construir a `GetStarted` solução, precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta do Azure Cosmos DB][cosmos-db-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) está disponível no GitHub.

Para restaurar as referências ao Azure Cosmos DB .NET SDK no Visual Studio, clique com o botão direito na solução no **Solution Explorer**e, em seguida, selecione **Restore NuGet Packages**. Em seguida, no ficheiro *App.config, * atualize os `EndPointUri` `PrimaryKey` valores descritos no [Passo 3: Ligue-se a uma conta DB Azure Cosmos](#Connect).

É isso, constrói-o e estás a caminho!

## <a name="next-steps"></a>Passos seguintes

* Quer um tutorial ASP.NET MVC mais complexo? Ver [Tutorial: Desenvolver uma aplicação web core MVC ASP.NET com a Azure Cosmos DB utilizando .NET SDK](sql-api-dotnet-application.md).
* Quer fazer testes de escala e desempenho com a Azure Cosmos DB? Ver [Testes de desempenho e escala com Azure Cosmos DB](performance-testing.md).
* Para aprender a monitorizar os pedidos, utilização e armazenamento da Azure Cosmos, consulte [as métricas de desempenho e armazenamento do Monitor em Azure Cosmos DB](monitor-accounts.md).
* Para executar consultas contra o nosso conjunto de dados de amostra, consulte o [Parque Desíduo de Consulta.](https://www.documentdb.com/sql/demo)
* Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
