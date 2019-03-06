---
title: Criar uma aplicação de consola .NET Core para gerir dados na conta do Azure Cosmos DB SQL API (SDK versão 3 pré-visualização)
description: Um tutorial que cria uma base de dados online e uma aplicação de consola em C# com o SDK de .NET Core da API SQL do Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 828d0ced775d46b0e966969acb3220d2a678c57b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440051"
---
# <a name="build-a-net-core-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>Criar uma aplicação de consola .NET Core para gerir dados na conta do Azure Cosmos DB SQL API (SDK versão 3 pré-visualização)

> [!div class="op_single_selector"]
>
> - [.NET core (pré-visualização)](sql-api-dotnet-core-get-started-preview.md)
> - [.NET Core](sql-api-dotnetcore-get-started.md)
> - [.NET (pré-visualização)](sql-api-dotnet-get-started-preview.md)
> - [.NET](sql-api-get-started.md)
> - [Java](sql-api-java-get-started.md)
> - [Async Java](sql-api-async-java-get-started.md)
> - [Node.js](sql-api-nodejs-get-started.md)

Bem-vindo ao Azure Cosmos DB SQL API introdução ao tutorial de .NET Core! Depois de seguir este tutorial, terá uma aplicação de consola .NET Core que cria e consulta recursos do Azure Cosmos DB. Este tutorial utiliza [versão 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do Azure Cosmos DB .NET SDK, os destinos [.NET Standard 2.0.](https://docs.microsoft.com/dotnet/standard/net-standard)

Este tutorial aborda:

> [!div class="checklist"]
>
> - Criar e ligar a uma conta do Cosmos do Azure
> - Configurar o seu projeto no Visual Studio
> - Criar uma base de dados e um contentor
> - Adicionar itens ao contentor
> - Consulta o contentor
> - Operações CRUD no item
> - Eliminar a base de dados

Não tem tempo para criar a aplicação? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started). Avance para a [secção Obter a solução completa](#GetSolution) para instruções rápidas.

Pretende compilar uma aplicação Xamarin iOS, Android ou Formulários com a API SQL e o SDK de .NET Core? Veja [Compilar aplicações móveis com o Xamarin e o Azure Cosmos DB](mobile-apps-with-xamarin.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

- Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Se estiver a desenvolver uma aplicação da Plataforma Universal do Windows (UWP), deve utilizar o **Visual Studio 2017 com a versão 15.4** ou superior. Certifique-se de que ativa o **desenvolvimento do Azure** carga de trabalho durante a configuração do Visual Studio.

  - Se estiver a trabalhar no MacOS ou em Linux, pode desenvolver aplicações .NET Core a partir da linha de comandos ao instalar o [SDK de .NET Core](https://www.microsoft.com/net/core#macos) para uma plataforma à sua escolha.

  - Se estiver a trabalhar em Windows, pode desenvolver aplicações .NET Core a partir da linha de comandos ao instalar o [SDK de .NET Core](https://www.microsoft.com/net/core#windows).

  - Pode utilizar o seu próprio editor ou transferir o [Visual Studio Code](https://code.visualstudio.com/), que é gratuito e funciona em Windows, Linux e MacOS.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a sua solução do Visual Studio](#SetupVS). Se estiver a utilizar o emulador do Azure Cosmos DB, siga os passos indicados em [emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e avance para [configurar seu projeto do Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Passo 2: Configurar o seu projeto do Visual Studio

1. Abra o **Visual Studio 2017** no seu computador.
1. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
1. Na **novo projeto** caixa de diálogo, selecione **Visual C#**   /  **aplicação de consola (.NET Core)**, nomeie o projeto e, em seguida, clique em **OK**.
   ![Captura de ecrã da janela novo projeto](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-new-project.png)
1. No **Explorador de Soluções**, clique com o botão direito do rato na sua nova aplicação de consola, que está sob a sua solução Visual Studio e, em seguida, clique em **Gerir Pacotes NuGet...**

   ![Captura de ecrã do Menu com Botão do Lado Direito para o Projeto](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-manage-nuget.png)

1. Na **NuGet** separador, clique em **procurar**e o tipo **Microsoft.Azure.Cosmos** na caixa de pesquisa.
1. Nos resultados, localize **Microsoft.Azure.Cosmos** e clique em **instalar**.
   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Captura de ecrã do NuGet Menu para encontrar o SDK de cliente do Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

   Se obtiver uma mensagem sobre a revisão das alterações para a solução, clique em **OK**. Se obtiver uma mensagem sobre a aceitação de licença, clique em **Aceito**.

Ótimo! Agora que concluímos a configuração, comecemos a escrever certos códigos. Pode encontrar um projeto de código completo deste tutorial em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started/).

## <a id="Connect"></a>Passo 3: Ligar a uma conta do Azure Cosmos DB

1. Em primeiro lugar, substitua as referências no início do seu C# aplicativo, o **Program.cs** ficheiro com essas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;
   ```

1. Agora, adicione essas constantes e variáveis em sua classe pública `Program`.

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
       private CosmosDatabase database;

       // The container we will create.
       private CosmosContainer container;

       // The name of the database and container we will create
       private string databaseId = "FamilyDatabase";
       private string containerId = "FamilyContainer";
   }
   ```

   Tenha em atenção de que se estiver familiarizado com a versão anterior do SDK do .NET, poderá ser utilizado para ver os termos "coleção' e 'document'. Como o Azure Cosmos DB suporta vários modelos de API, versão 3.0 + do SDK do .NET usa os termos genéricos "contentor" e "item". Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. [Saiba mais sobre as bases de dados, contentores e itens.](databases-containers-items.md)

1. Obter o URL de ponto final e a chave primária a partir da [portal do Azure](https://portal.azure.com).

   No portal do Azure, navegue até à sua conta do Azure Cosmos DB e, em seguida, clique em **Chaves**.

   Copie o URI a partir do portal e cole-a na `<your endpoint URL>` no `Program.cs` ficheiro. Copie a chave primária do portal e cole-o em `<your primary key>`.

   ![Captura de ecrã para obter as chaves do Azure Cosmos DB a partir do portal do Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Em seguida, vamos criar uma nova instância do `CosmosClient` e configurar alguns scaffolding para nosso programa.

   Abaixo da **Main** método, adicione uma nova tarefa assíncrona designada **GetStartedDemoAsync**, que irá criar uma instância de nossa nova `CosmosClient`. Nós usaremos **GetStartedDemoAsync** como ponto de entrada que chama os métodos que operam em recursos do Azure Cosmos DB.

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

1. Adicione o seguinte código para executar o **GetStartedDemoAsync** tarefa assíncrona a partir do **Main** método. O método **Principal** irá capturar as exceções e escrevê-las na consola.

   ```csharp
   public static async Task Main(string[] args)
   {
       // ADD THIS PART TO YOUR CODE
       try
       {
           Console.WriteLine("Beginning operations...\n");
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
   ```

1. Selecione **F5** para executar a sua aplicação. O resultado da janela de consola apresenta a mensagem `End of demo, press any key to exit.` que confirma que foi efetuada uma ligação ao Azure Cosmos DB. Em seguida, pode fechar a janela da consola.

Parabéns! Ligou com êxito a uma conta do Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados

Uma base de dados pode ser criado utilizando o [ **CreateDatabaseIfNotExistsAsync** ](https://aka.ms/CosmosDotnetAPIDocs) ou [ **CreateDatabaseAsync** ](https://aka.ms/CosmosDotnetAPIDocs) função do **CosmosDatabases** classe. As bases de dados são os contentores lógicos dos itens particionados em contentores.

1. Copie e cole o **CreateDatabase** método abaixo sua **GetStartedDemoAsync** método. **CreateDatabase** irá criar uma nova base de dados com o id `FamilyDatabase` se ainda não exista, com o id especificado a partir do `databaseId` campo.

   ```csharp
   /*
       Create the database if it does not exist
   */
   private async Task CreateDatabase()
   {
       // Create a new database
       this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
       Console.WriteLine("Created Database: {0}\n", this.database.Id);
   }
   ```

1. Copie e cole o código a seguir, onde instanciado CosmosClient para chamar o **CreateDatabase** método que acabou de adicionar.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

       //ADD THIS PART TO YOUR CODE
       await this.CreateDatabase();
   }
   ```

   Neste momento, seu código deverá agora ser semelhante a esta, com o seu ponto final e a chave primária preenchidos. Observe que o espaço de nomes serão diferentes com base no nome do seu projeto.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;

   namespace CosmosGettingStartedDotnetCoreTutorial
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
           private CosmosDatabase database;

           // The container we will create.
           private CosmosContainer container;

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

           /*
               Entry point to call methods that operate on Azure Cosmos DB resources in this sample
           */
           public async Task GetStartedDemoAsync()
           {
               // Create a new instance of the Cosmos Client
               this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
               await this.CreateDatabase();
           }

           /*
               Create the database if it does not exist
           */
           private async Task CreateDatabase()
           {
               // Create a new database
               this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
               Console.WriteLine("Created Database: {0}\n", this.database.Id);
           }
       }
   }
   ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Criou uma base de dados do Azure Cosmos DB com êxito.

## <a id="CreateColl"></a>Passo 5: Criar um contentor

> [!WARNING]
> Chamando o método **CreateContainerIfNotExistsAsync** irá criar um novo contentor, que tem sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).

Um contentor pode ser criado utilizando o [ **CreateContainerIfNotExistsAsync** ](https://aka.ms/CosmosDotnetAPIDocs) ou [ **CreateContainerAsync** ](https://aka.ms/CosmosDotnetAPIDocs) funcionem no **CosmosContainers** classe. Um contentor consiste em itens (o que, no caso da API de SQL é documentos JSON) e associados a lógica de aplicação do lado do servidor de JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo utilizador e acionadores.

1. Copie e cole o **CreateContainer** método abaixo sua **CreateDatabase** método. **CreateContainer** irá criar um novo contentor com o id `FamilyContainer` se ainda não exista, com o id especificado a partir do `containerId` campo.

   ```csharp
   /*
       Create the container if it does not exist.
       Specify "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
   */
   private async Task CreateContainer()
   {
       // Create a new container
       this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
       Console.WriteLine("Created Container: {0}\n", this.container.Id);
   }
   ```

1. Copie e cole o código a seguir, onde instanciado CosmosClient para chamar o **CreateContainer** método que acabou de adicionar.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();

       //ADD THIS PART TO YOUR CODE
       await this.CreateContainer();
   }
   ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Criou um contentor do Azure Cosmos DB com êxito.

## <a id="CreateDoc"></a>Passo 6: Adicionar itens ao contentor

Um item pode ser criado utilizando o [ **CreateItemAsync** ](https://aka.ms/CosmosDotnetAPIDocs) função dos **CosmosItems** classe. Quando é utilizada a API SQL, os itens são projetados como documentos, que são conteúdos JSON definidos pelo utilizador (arbitrários). Pode agora inserir um item no seu contentor do Azure Cosmos DB.

Em primeiro lugar, temos de criar uma classe **Família** que irá representar objetos armazenados no Azure Cosmos DB neste exemplo. Também iremos criar subclasses **Principal**, **Subordinado**, **Animal de estimação** e **Endereço** utilizadas dentro da **Família**. Tenha em atenção que os documentos têm de ter um **Id** propriedade serializado como **id** no JSON.

1.  Selecione **Ctrl + Shift + A** para abrir o **Add New Item** caixa de diálogo. Adicionar uma nova classe **Family.cs** ao seu projeto.

    ![Captura de ecrã da adição de uma nova classe de Family.cs no projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1.  Copie e cole o **família**, **principal**, **filho**, **animal de estimação**, e **endereço** classe **Family.cs**. Observe que o espaço de nomes serão diferentes com base no nome do seu projeto.

    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStartedDotnetCoreTutorial
    {
        public class Family
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
            public string LastName { get; set; }
            public Parent[] Parents { get; set; }
            public Child[] Children { get; set; }
            public Address Address { get; set; }
            public bool IsRegistered { get; set; }
            public override string ToString()
            {
                return JsonConvert.SerializeObject(this);
            }
        }

        public class Parent
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
        }

        public class Child
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
            public string Gender { get; set; }
            public int Grade { get; set; }
            public Pet[] Pets { get; set; }
        }

        public class Pet
        {
            public string GivenName { get; set; }
        }

        public class Address
        {
            public string State { get; set; }
            public string County { get; set; }
            public string City { get; set; }
        }
    }
    ```

1.  Navegue de volta para **Program.cs** e adicione o **AddItemsToContainer** método em seu **CreateContainer** método.
    O código verifica para se certificar de que um item com o mesmo id ainda não existir antes de criá-lo. Podemos irá inserir dois itens, um para a família Andersen e a família Wakefield.

    ```csharp
     /*
         Add Family items to the container
     */
     private async Task AddItemsToContainer()
     {
         // Create a family object for the Andersen family
         Family andersenFamily = new Family
         {
             Id = "Andersen.1",
             LastName = "Andersen",
             Parents = new Parent[]
             {
                 new Parent { FirstName = "Thomas" },
                 new Parent { FirstName = "Mary Kay" }
             },
             Children = new Child[]
             {
                 new Child
                 {
                     FirstName = "Henriette Thaulow",
                     Gender = "female",
                     Grade = 5,
                     Pets = new Pet[]
                     {
                         new Pet { GivenName = "Fluffy" }
                     }
                 }
             },
             Address = new Address { State = "WA", County = "King", City = "Seattle" },
             IsRegistered = true
         };

         // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object.
         CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

         if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
             andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
         }

         // Create a family object for the Wakefield family
         Family wakefieldFamily = new Family
         {
             Id = "Wakefield.7",
             LastName = "Wakefield",
             Parents = new Parent[]
             {
                 new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                 new Parent { FamilyName = "Miller", FirstName = "Ben" }
             },
             Children = new Child[]
             {
                 new Child
                 {
                     FamilyName = "Merriam",
                     FirstName = "Jesse",
                     Gender = "female",
                     Grade = 8,
                     Pets = new Pet[]
                     {
                         new Pet { GivenName = "Goofy" },
                         new Pet { GivenName = "Shadow" }
                     }
                 },
                 new Child
                 {
                     FamilyName = "Miller",
                     FirstName = "Lisa",
                     Gender = "female",
                     Grade = 1
                 }
             },
             Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
             IsRegistered = false
         };

         // Read the item to see if it exists
         CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

         if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
             wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
         }
     }
    
    ```

1.  Adicione uma chamada para `AddItemsToContainer` no `GetStartedDemoAsync` método.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase();
        await this.CreateContainer();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainer();
    }
    ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Criou dois itens de Azure Cosmos DB com êxito.

## <a id="Query"></a>Passo 7: Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB suporta [consultas](sql-api-sql-query.md) extensas de documentos JSON armazenados em cada coleção. O código de exemplo seguinte mostra como executar uma consulta os itens que inserimos no passo anterior.

1. Copie e cole o **RunQuery** método abaixo sua **AddItemsToContainer** método.

   ```csharp
   /*
       Run a query (using Azure Cosmos DB SQL syntax) against the container
   */
   private async Task RunQuery()
   {
       var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
       var partitionKeyValue = "Andersen";

       Console.WriteLine("Running query: {0}\n", sqlQueryText);

       CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
       CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

       List<Family> families = new List<Family>();

       while (queryResultSetIterator.HasMoreResults)
       {
           CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
           foreach (Family family in currentResultSet)
           {
               families.Add(family);
               Console.WriteLine("\tRead {0}\n", family);
           }
       }
   }
   ```

1. Adicione uma chamada para `RunQuery` no `GetStartedDemoAsync` método.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();

       //ADD THIS PART TO YOUR CODE
       await this.RunQuery();
   }
   ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Consultou com êxito em relação a um contentor do Azure Cosmos DB.

## <a id="ReplaceItem"></a>Passo 8: Substituir um item JSON

Agora, vamos atualizar um item no Azure Cosmos DB.

1. Copie e cole o **ReplaceFamilyItem** método abaixo sua **RunQuery** método. Tenha em atenção de que estamos a alterar o `IsRegistered` propriedade da família e o `Grade` de um dos filhos.

   ```csharp
   /*
   Update an item in the container
   */
   private async Task ReplaceFamilyItem()
   {
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
       var itemBody = wakefieldFamilyResponse.Resource;

       // update registration status from false to true
       itemBody.IsRegistered = true;
       // update grade of child
       itemBody.Children[0].Grade = 6;

       // replace the item with the updated content
       wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
       Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
   }
   ```

1. Adicione uma chamada para `ReplaceFamilyItem` no `GetStartedDemo` método.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();

       //ADD THIS PART TO YOUR CODE
       await this.ReplaceFamilyItem();
   }
   ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Substituiu um item do Azure Cosmos DB com êxito.

## <a id="DeleteDocument"></a>Passo 9: Eliminar item

Agora, iremos eliminar um item no Azure Cosmos DB.

1. Copie e cole o **DeleteFamilyItem** método abaixo sua **ReplaceFamilyItem** método.

   ```csharp
   /*
   Delete an item in the container
   */
   private async Task DeleteFamilyItem()
   {
       var partitionKeyValue = "Wakefield";
       var familyId = "Wakefield.7";

       // Delete an item. Note we must provide the partition key value and id of the item to delete
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
       Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
   }
   ```

1. Adicione uma chamada para `DeleteFamilyItem` no `GetStartedDemo` método.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteFamilyItem();
   }
   ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Eliminou um item do Azure Cosmos DB com êxito.

## <a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados

Agora, iremos eliminar nosso banco de dados. A eliminar a base de dados criada remove a base de dados e todos os recursos subordinados (contentores, itens e qualquer procedimentos armazenados, funções definidas pelo utilizador e acionadores). Podemos também será descartar a **CosmosClient** instância.

1. Copie e cole o **DeleteDatabaseAndCleanup** método abaixo sua **DeleteFamilyItem** método.

   ```csharp
   /*
   Delete the database and dispose of the Cosmos Client instance
   */
   private async Task DeleteDatabaseAndCleanup()
   {
       CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
       // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

       Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

       //Dispose of CosmosClient
       this.cosmosClient.Dispose();
   }
   ```

1. Adicione uma chamada para `DeleteDatabaseAndCleanup` no `GetStartedDemo` método.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();
       await this.DeleteFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteDatabaseAndCleanup();
   }
   ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Eliminou bases de dados do Azure Cosmos DB com êxito.

## <a id="Run"></a>Passo 11: Executar o C# aplicação de consola tudo!

Selecione **F5** no Visual Studio para criar a aplicação no modo de depuração.

Deverá ver a saída de toda a obter a aplicação de introdução numa janela de consola. O resultado apresentará os resultados das consultas que adicionámos e deverá corresponder ao texto de exemplo abaixo.

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

Para criar a solução GetStarted que contém todos os exemplos deste artigo, deverá ter o seguinte:

- Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
- Uma [conta do Azure Cosmos DB][cosmos-db-create-account].
- A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started) está disponível no GitHub.

Para restaurar as referências para a API de SQL para o SDK do Azure Cosmos DB .NET Core no Visual Studio, clique com botão direito a **GetStarted** solução no Explorador de soluções e, em seguida, selecione **restaurar pacotes NuGet**. Em seguida, no **Program.cs** de ficheiros, atualize o `EndpointUri` e `PrimaryKey` os valores, conforme descrito na [ligar a uma conta do Azure Cosmos DB](#Connect).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como criar uma aplicação .NET Core para gerir os dados da API de SQL do Azure Cosmos DB. Agora, pode importar dados adicionais à sua conta do Cosmos DB.

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
