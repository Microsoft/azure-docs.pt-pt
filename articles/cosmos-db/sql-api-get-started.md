---
title: Criar um aplicativo de console .NET para gerenciar dados em Azure Cosmos DB conta da API do SQL
description: Saiba como criar Azure Cosmos DB recursos da API do SQL usando C# um aplicativo de console.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 25275aeb6637d50379b7c71e87be9ecffce269db
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614682"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Criar um aplicativo de console .NET para gerenciar dados em Azure Cosmos DB conta da API do SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Bem-vindo ao tutorial de introdução à API do SQL Azure Cosmos DB. Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do Cosmos DB. Este tutorial usa a [versão 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do SDK do .net Azure Cosmos DB, que pode ser direcionado para [.NET Framework](https://dotnet.microsoft.com/download) ou [.NET Core](https://dotnet.microsoft.com/download).

Este tutorial aborda:

> [!div class="checklist"]
> * Criar e ligar a uma conta do Cosmos do Azure
> * Configurando seu projeto no Visual Studio
> * Criando um banco de dados e um contêiner
> * Adicionar itens ao contentor
> * Consulta o contentor
> * Operações CRUD no item
> * Eliminar a base de dados

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Vá para a [seção obter a solução completa do tutorial](#GetSolution) para obter instruções rápidas.

Agora comecemos!

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB
Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a sua solução do Visual Studio](#SetupVS). Se você estiver usando o emulador de Azure Cosmos DB, siga as etapas em [Azure Cosmos DB emulador](local-emulator.md) para configurar o emulador e pule para [configurar seu projeto do Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Etapa 2: Configurar seu projeto do Visual Studio
1. Abra o **Visual Studio 2017** no seu computador.
1. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
1. Na caixa de diálogo **novo projeto** ,  / selecione aplicativo de console do **Visual C#**  **(.NET Framework)** , nomeie o projeto e clique em **OK**.
    ![Captura de tela da janela novo projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Para destino do .NET Core, na caixa de diálogo **novo projeto** , selecione  / aplicativo de console **Visual C#**  **(.NET Core)** , nomeie o projeto e clique em **OK**

1. No **Explorador de Soluções**, clique com o botão direito do rato na sua nova aplicação de consola, que está sob a sua solução Visual Studio e, em seguida, clique em **Gerir Pacotes NuGet...**

    ![Captura de ecrã do Menu com Botão do Lado Direito para o Projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Na **NuGet** separador, clique em **procurar**e o tipo **Microsoft.Azure.Cosmos** na caixa de pesquisa.
1. Nos resultados, localize **Microsoft.Azure.Cosmos** e clique em **instalar**.
   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Captura de tela do menu do NuGet para localizar Azure Cosmos DB SDK do cliente](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Se obtiver uma mensagem sobre a revisão das alterações para a solução, clique em **OK**. Se obtiver uma mensagem sobre a aceitação de licença, clique em **Aceito**.

Ótimo! Agora que concluímos a configuração, comecemos a escrever certos códigos. Pode encontrar um projeto de código completo deste tutorial em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Etapa 3: Conectar-se a uma conta de Azure Cosmos DB
1. Primeiro, substitua as referências no início do C# aplicativo, no arquivo **Program.cs** com estas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Agora, adicione essas constantes e variáveis à sua classe ``Program``pública.

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

    Tenha em atenção de que se estiver familiarizado com a versão anterior do SDK do .NET, poderá ser utilizado para ver os termos "coleção' e 'document'. Como o Azure Cosmos DB suporta vários modelos de API, versão 3.0 + do SDK do .NET usa os termos genéricos "contentor" e "item". Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. [Saiba mais sobre as bases de dados, contentores e itens.](databases-containers-items.md)

1. Recupere a URL do ponto de extremidade e a chave primária do [portal do Azure](https://portal.azure.com).

    No portal do Azure, navegue até à sua conta do Azure Cosmos DB e, em seguida, clique em **Chaves**.

    Copie o URI do portal e cole- `<your endpoint URL>` ```Program.cs``` o no arquivo. Copie a chave primária do portal e cole-a no `<your primary key>`.

   ![Captura de tela para obter Azure Cosmos DB chaves de portal do Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Em seguida, criaremos uma nova instância do ```CosmosClient``` e configuraremos alguns scaffolding para nosso programa.

    Abaixo do método **Main** , adicione uma nova tarefa assíncrona chamada **GetStartedDemoAsync**, que criará uma ```CosmosClient```instância do nosso novo. Usaremos **GetStartedDemoAsync** como o ponto de entrada que chama métodos que operam em Azure Cosmos DB recursos.

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

1. Adicione o código a seguir para executar a tarefa assíncrona **GetStartedDemoAsync** do seu método **principal** . O método **Principal** irá capturar as exceções e escrevê-las na consola.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecione **F5** para executar o aplicativo. A saída da janela do console exibe `End of demo, press any key to exit.` a mensagem confirmando que a conexão com Azure Cosmos DB foi feita. Em seguida, pode fechar a janela da consola.

Parabéns! Você se conectou com êxito a uma conta de Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados
Um banco de dados pode ser criado usando a ``CosmosClient`` função [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) ou [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) da classe. As bases de dados são os contentores lógicos dos itens particionados em contentores.

1. Copie e cole o método **CreateDatabaseAsync** abaixo do método **GetStartedDemoAsync** . **CreateDatabaseAsync** criará um novo banco de dados ``FamilyDatabase`` com a ID se ele ainda não existir, com a ``databaseId`` ID especificada do campo. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Copie e cole o código abaixo, onde você instanciou o CosmosClient para chamar o método **CreateDatabaseAsync** que você acabou de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Neste ponto, o código agora deve ser assim, com o ponto de extremidade e a chave primária preenchida.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
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

Selecione **F5** para executar o aplicativo.

Parabéns! Você criou um banco de dados Cosmos do Azure com êxito.  

## <a id="CreateColl"></a>Etapa 5: Criar um contentor
> [!WARNING]
> Chamar o método **CreateContainerIfNotExistsAsync** criará um novo contêiner, que tem implicações de preço. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Um contêiner pode ser criado usando a função [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) ou [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) na classe **CosmosDatabase** . Um contêiner consiste em itens (documentos JSON se a API do SQL) e lógica de aplicativo associada no lado do servidor em JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo usuário e gatilhos.

1. Copie e cole o método **CreateContainerAsync** abaixo do método **CreateDatabaseAsync** . **CreateContainerAsync** criará um novo contêiner com a ``FamilyContainer`` ID se ele ainda não existir, com a ``containerId`` ID especificada do campo particionado por ``LastName`` propriedade.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copie e cole o código abaixo, onde você instanciou o CosmosClient para chamar o método CreateContainer que você acabou de adicionar.

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

   Selecione **F5** para executar o aplicativo.

Parabéns! Você criou um contêiner Cosmos do Azure com êxito.  

## <a id="CreateDoc"></a>Etapa 6: Adicionar itens ao contêiner
Um item pode ser criado usando a função [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) da classe **CosmosContainer** . Quando é utilizada a API SQL, os itens são projetados como documentos, que são conteúdos JSON definidos pelo utilizador (arbitrários). Agora você pode inserir um item em seu contêiner Cosmos do Azure.

Primeiro, vamos criar uma classe **Family** que representará os objetos armazenados em Azure Cosmos DB neste exemplo. Também iremos criar subclasses **Principal**, **Subordinado**, **Animal de estimação** e **Endereço** utilizadas dentro da **Família**. O item de observação deve ter uma propriedade de **ID** serializada como **ID** em JSON.

1. Selecione **Ctrl + Shift + a** para abrir a caixa de diálogo **Adicionar novo item** . Adicione uma nova classe **Family.cs** ao seu projeto.

    ![Captura de tela da adição de uma nova classe Family.cs no projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copie e cole a classe **família**, **pai**, **filho**, **animal**de estimação e **endereço** em **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Navegue de volta para **Program.cs** e adicione o método **AddItemsToContainerAsync** em seu método **CreateContainerAsync** .
O código verifica se um item com a mesma ID ainda não existe antes de criá-lo. Inseriremos dois itens, um para a família Andersen e a família Barros.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Adicione uma chamada para ``AddItemsToContainerAsync`` ``GetStartedDemoAsync`` no método.

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

Selecione **F5** para executar o aplicativo.

Parabéns! Você criou dois itens do Azure cosmos com êxito.  

## <a id="Query"></a>Etapa 7: Recursos de Azure Cosmos DB de consulta
O Azure Cosmos DB suporta [consultas](sql-api-sql-query.md) extensas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra como executar uma consulta em relação aos itens que inserimos na etapa anterior.

1. Copie e cole o método **QueryItemsAsync** abaixo do método **AddItemsToContainerAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adicione uma chamada para ``QueryItemsAsync`` ``GetStartedDemoAsync`` no método.

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

Selecione **F5** para executar o aplicativo.

Parabéns! Você consultou com êxito um contêiner Cosmos do Azure.

## <a id="ReplaceItem"></a>Etapa 8: Substituir um item JSON
Agora, atualizaremos um item no Azure Cosmos DB.

1. Copie e cole o método **ReplaceFamilyItemAsync** abaixo do método **QueryItemsAsync** . Observe que estamos alterando ``IsRegistered`` a propriedade da família e o ``Grade`` de um dos filhos.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adicione uma chamada para ``ReplaceFamilyItemAsync`` ``GetStartedDemoAsync`` no método.

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

   Selecione **F5** para executar o aplicativo.

Parabéns! Você substituiu um item Cosmos do Azure com êxito.

## <a id="DeleteDocument"></a>Etapa 9: Eliminar item
Agora, excluiremos um item em Azure Cosmos DB.

1. Copie e cole o método **DeleteFamilyItemAsync** abaixo do método **ReplaceFamilyItemAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adicione uma chamada para ``DeleteFamilyItemAsync`` ``GetStartedDemoAsync`` no método.

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

Selecione **F5** para executar o aplicativo.

Parabéns! Você excluiu com êxito um item Cosmos do Azure.

## <a id="DeleteDatabase"></a>Etapa 10: Excluir o banco de dados
Agora, iremos excluir nosso banco de dados. Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (contêineres, itens e quaisquer procedimentos armazenados, funções definidas pelo usuário e gatilhos). Também descartaremos a instância **CosmosClient** .

1. Copie e cole o método **DeleteDatabaseAndCleanupAsync** abaixo do método **DeleteFamilyItemAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adicione uma chamada para ``DeleteDatabaseAndCleanupAsync`` ``GetStartedDemoAsync`` no método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Selecione **F5** para executar o aplicativo.

Parabéns! Você excluiu com êxito um banco de dados Cosmos do Azure.

## <a id="Run"></a>Etapa 11: Execute seu C# aplicativo de console juntos!
Selecione F5 no Visual Studio para compilar e executar o aplicativo no modo de depuração.

Você deve ver a saída de todo o seu aplicativo em uma janela de console. O resultado apresentará os resultados das consultas que adicionámos e deverá corresponder ao texto de exemplo abaixo.

```
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
Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir os exemplos de código, pode obtê-los a partir do [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

Para criar a solução GetStarted, irá precisar do seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta de Azure Cosmos DB][cosmos-db-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) está disponível no GitHub.

Para restaurar as referências para o SDK do .NET Azure Cosmos DB no Visual Studio, clique com o botão direito do mouse na solução getstarted no Gerenciador de soluções e clique em **restaurar pacotes NuGet**. Em seguida, no arquivo app. config, atualize os valores de EndPointUri e PrimaryKey, conforme descrito em [conectar-se a uma conta de Azure Cosmos DB](#Connect).

É isso, crie e você está no caminho!

## <a name="next-steps"></a>Passos Seguintes
* Quer um tutorial ASP.NET MVC mais complexo? Consulte [o tutorial do MVC do ASP.net: Desenvolvimento de aplicativos Web com](sql-api-dotnet-application-preview.md)Azure Cosmos DB.
* Pretende testar o dimensionamento e desempenho com o Azure Cosmos DB? Veja [Testar o Desempenho e o Dimensionamento com o Azure Cosmos DB](performance-testing.md)
* Saiba como [monitorizar pedidos, utilização e armazenamento do Azure Cosmos DB](monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
