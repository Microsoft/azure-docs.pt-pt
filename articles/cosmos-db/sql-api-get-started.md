---
title: Criar uma aplicação de consola .NET para gerir dados na conta do Azure Cosmos DB SQL API
description: Um tutorial que cria uma base de dados online e C# aplicação de consola com a API de SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 64aef17663fdc28a467172bbe8954fc06fdb7ff0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680404"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Criar uma aplicação de consola .NET para gerir dados na conta do Azure Cosmos DB SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (pré-visualização)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (pré-visualização)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Tutorial de introdução de bem-vindo ao obter a API de SQL do Azure Cosmos DB. Depois de concluir este tutorial, terá uma aplicação de consola que cria e consulta recursos do Azure Cosmos DB.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
>
> - Criar uma conta do Azure Cosmos DB e ligá-la
> - Configurar uma solução do Visual Studio
> - Criar uma base de dados
> - Criar uma coleção
> - Criar documentos JSON
> - Consultar a coleção
> - Atualizar um documento JSON
> - Eliminar um documento
> - Eliminar a base de dados

## <a name="prerequisites"></a>Pré-requisitos

Visual Studio 2017 com o fluxo de trabalho de desenvolvimento do Azure instalado:
- Pode transferir e utilizar o **gratuita** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio. 

Uma subscrição do Azure ou a conta de avaliação gratuita do Cosmos DB:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Se estiver a utilizar o emulador do Azure Cosmos DB, siga os passos indicados em [emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador. Em seguida, inicie o tutorial em [configurar a solução do Visual Studio](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Obter a solução concluída

Se não tiver tempo para concluir o tutorial ou simplesmente os exemplos de código, pode baixar a solução completa de [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Para executar a solução completa transferida: 

1. Certifique-se de que tem o [pré-requisitos](#prerequisites) instalado. 
1. Abra o transferido *GetStarted.sln* arquivo da solução no Visual Studio.
1. Na **Explorador de soluções**, clique com botão direito a **GetStarted** do projeto e selecione **gerir pacotes NuGet**.
1. Sobre o **NuGet** separador, selecione **restaurar** para restaurar as referências para o SDK de .NET do Azure Cosmos DB.
1. Na *App. config* de ficheiros, atualize o `EndpointUrl` e `PrimaryKey` os valores, conforme descrito no [ligar à conta do Azure Cosmos DB](#Connect) secção.
1. Selecione **depurar** > **iniciar sem depuração** ou prima **Ctrl**+**F5** para compilar e executar a aplicação.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Siga estas instruções para criar uma conta do Azure Cosmos DB no portal do Azure. Se já tiver uma conta do Azure Cosmos DB a utilizar, avançar diretamente para [configurar a solução do Visual Studio](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurar a solução do Visual Studio

1. No Visual Studio 2017, selecione **arquivo** > **New** > **projeto**.
   
1. Na **novo projeto** caixa de diálogo, selecione **Visual C#**   >  **aplicação de consola (.NET Framework)**, nomeie o projeto *AzureCosmosDBApp* e, em seguida, selecione **OK**.
   
   ![Captura de ecrã da janela Novo Projeto](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. Na **Explorador de soluções**, clique com botão direito a **AzureCosmosDBApp** do projeto e selecione **gerir pacotes NuGet**.
   
   ![Menu de contexto do projeto](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. Sobre o **NuGet** separador, selecione **procurar**e introduza *documentdb do azure* na caixa de pesquisa.
   
1. Localize e selecione **Microsoft.Azure.DocumentDB**e selecione **instalar** se ainda não estiver instalado.
   
   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).

   > [!NOTE]
   > Se estiver a utilizar o .NET Core, veja [os documentos de .NET Core](./sql-api-dotnetcore-get-started.md).

   ![Captura de ecrã do NuGet Menu para encontrar o SDK de cliente do Azure Cosmos DB](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Se obtiver uma mensagem sobre a pré-visualizar as alterações à solução, selecione **OK**. Se obtiver uma mensagem sobre a aceitação da licença, selecione **aceito**.

## <a id="Connect"></a>Ligar à conta do Azure Cosmos DB

Agora, comece a escrever certos códigos. O complete *Project.cs* de ficheiros para este tutorial é na [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. Na **Explorador de soluções**, selecione *Program.cs*e no editor de código, adicione as seguintes referências para o início do ficheiro:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Em seguida, adicione as seguintes duas constantes e a `client` variável à `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. O URL de ponto final e a chave primária permitir que a sua aplicação ligar à sua conta do Azure Cosmos DB e a conta do Azure Cosmos DB confie na ligação. Copiar as chaves a partir da [portal do Azure](https://portal.azure.com)e cole-os no seu código. 

   
   1. No seu Azure Cosmos DB conta de navegação esquerdo, selecione **chaves**.
      
      ![Ver e copiar chaves de acesso no portal do Azure](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. Sob **chaves de leitura / escrita**, copiar o **URI** com o botão Copiar no lado direito de valor e cole-a na `<your endpoint URL>` no *Program.cs*. Por exemplo: 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Copiar o **chave primária** valor e cole-a na `<your primary key>` no *Program.cs*. Por exemplo: 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Depois do `Main` método, adicione uma nova tarefa assíncrona designada `GetStartedDemo`, que instancia um novo `DocumentClient` chamado `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. Adicione o seguinte código para o `Main` método para executar o `GetStartedDemo` tarefas. O `Main` método captura exceções e escreve-as na consola.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Prima **F5** para executar a aplicação. 
   
1. Quando vir a mensagem **final da demonstração, pressione qualquer tecla para sair** na janela da consola, significa que a ligação foi concluída com êxito. Prima qualquer tecla para fechar a janela de consola. 

Que ligou com êxito à sua conta do Azure Cosmos DB. Agora, trabalhar com alguns recursos do Azure Cosmos DB.  

## <a name="create-a-database"></a>Criar uma base de dados

Azure Cosmos DB [base de dados](databases-containers-items.md#azure-cosmos-databases) é o contentor lógico de armazenamento de documentos JSON particionado em coleções. Criar uma base de dados utilizando o [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) método o `DocumentClient` classe. 

1. Antes de adicionar o código para a criação de uma base de dados, adicione um método de programa auxiliar para escrever na consola. Copie e cole o seguinte procedimento `WriteToConsoleAndPromptToContinue` método após o `GetStartedDemo` método em seu código.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Copie e cole a seguinte linha ao seu `GetStartedDemo` método, após o `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` linha. Este código cria uma base de dados com o nome `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Prima **F5** para executar a aplicação.

Criou uma base de dados do Azure Cosmos DB com êxito. Pode ver a base de dados a [portal do Azure](https://portal.azure.com) ao selecionar **Data Explorer** na sua navegação à esquerda de conta do Azure Cosmos DB. 

## <a id="CreateColl"></a>Criar uma coleção

Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript. Criar uma coleção com o [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) método o `DocumentClient` classe. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** cria uma nova coleção com débito reservado, que tem sobre os preços. Para obter mais detalhes, visite o [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Copie e cole o seguinte código no seu `GetStartedDemo` método após o `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` linha. Este código cria uma coleção de documentos com o nome `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Prima **F5** para executar a aplicação.

Criou uma coleção de documentos do Azure Cosmos DB com êxito. Pode ver a coleção em sua **FamilyDB** base de dados no **Data Explorer** no portal do Azure.  

## <a id="CreateDoc"></a>Criar documentos JSON

Os documentos são conteúdos JSON arbitrários, definidas pelo utilizador. Documentos têm de ter uma propriedade de ID serializada como `id` em JSON. Criar documentos utilizando o [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) método o `DocumentClient` classe. 

> [!TIP]
> Se já tiver dados que pretende armazenar na sua base de dados, pode utilizar o Azure Cosmos DB [ferramenta de migração de dados](import-data.md) importá-lo.
>

O código a seguir cria e insere dois documentos para a sua coleção de base de dados. Em primeiro lugar, crie uma `Family` classe, e `Parent`, `Child`, `Pet`, e `Address` subclasses para utilizar na `Family`. Em seguida, crie um `CreateFamilyDocumentIfNotExists` método e, em seguida, crie e insira dois documentos. 

1. Copie e cole o seguinte procedimento `Family`, `Parent`, `Child`, `Pet`, e `Address` classes após o `WriteToConsoleAndPromptToContinue` método em seu código.
   
   ```csharp
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
   ```
   
1. Copie e cole o seguinte procedimento `CreateFamilyDocumentIfNotExists` método após o `Address` classe que acabou de adicionar.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Copie e cole o seguinte código no final da sua `GetStartedDemo` método, após o `await client.CreateDocumentCollectionIfNotExistsAsync` linha. Este código cria e insere dois documentos, um para as famílias Andersen e Wakefield.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Prima **F5** para executar a aplicação.

Criou dois documentos do Azure Cosmos DB com êxito. Pode ver os documentos em seu **FamilyDB** base de dados e **FamilyCollection** coleção na **Data Explorer** no portal do Azure.   

![Diagrama que ilustra a relação hierárquica entre a conta, base de dados online, a coleção e os documentos](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB suporta [consultas](how-to-sql-query.md) extensas de documentos JSON armazenados em coleções. O código de exemplo seguinte utiliza sintaxe LINQ e do Azure Cosmos DB SQL para executar uma consulta contra os documentos de amostra.

1. Copie e cole o seguinte procedimento `ExecuteSimpleQuery` método após o `CreateFamilyDocumentIfNotExists` método em seu código.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Copie e cole o seguinte código no final da sua `GetStartedDemo` método, após o `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` linha.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Prima **F5** para executar a aplicação.

A consulta anterior devolve o item completo para a família Andersen. Já consultados com êxito em relação a uma coleção do Azure Cosmos DB.

O diagrama seguinte ilustra como a sintaxe de consulta do Azure Cosmos DB SQL chama na coleção. A mesma lógica aplicada à consulta LINQ.

![Diagrama que ilustra o âmbito e o significado da consulta utilizada pelo tutorial NoSQL para criar uma aplicação de consola C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

O [FROM](how-to-sql-query.md#FromClause) palavra-chave na consulta SQL é opcional, porque as consultas do Azure Cosmos DB já estão confinadas a uma única coleção. Pode alternar `FROM Families f` com `FROM root r`, ou qualquer outro nome de variável que escolher. O Azure Cosmos DB irá inferir que `Families`, `root`, ou o nome da variável que escolher refere-se para o conjunto atual.

## <a id="ReplaceDocument"></a>Atualizar um documento JSON

API de SQL do Azure Cosmos DB suporta a atualização e substituição de documentos JSON.  

1. Copie e cole o seguinte procedimento `ReplaceFamilyDocument` método após o `ExecuteSimpleQuery` método em seu código.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Copie e cole o seguinte código no final da sua `GetStartedDemo` método, após o `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` linha. O código atualiza os dados em um dos documentos e, em seguida, executa a consulta novamente para mostrar o documento alterado.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Prima **F5** para executar a aplicação.

O resultado da consulta mostra que o `Grade` para menor a família Andersen atualizado a partir do `5` para `6`. Já atualizados e substituídos documentos do Azure Cosmos DB com êxito. 

## <a id="DeleteDocument"></a>Eliminar um documento JSON

API de SQL do Azure Cosmos DB suporta a eliminação de documentos JSON.  

1. Copie e cole o seguinte procedimento `DeleteFamilyDocument` método após o `ReplaceFamilyDocument` método.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Copie e cole o seguinte código no final da sua `GetStartedDemo` método, após o segundo `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` linha.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Prima **F5** para executar a aplicação.

Foi eliminado com êxito os documentos do Azure Cosmos DB. 

## <a id="DeleteDatabase"></a>Eliminar a base de dados

Elimine a base de dados que criou para remover ele e todos os recursos de subordinado, incluindo a coleção e documentos. 

1. Copie e cole o seguinte código no final da sua `GetStartedDemo` método, após o `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` linha. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Prima **F5** para executar a aplicação.

Foi eliminado com êxito a base de dados do Azure Cosmos DB. Pode ver na **Data Explorer** para a sua conta do Azure Cosmos DB que a base de dados FamilyDB é eliminado. 

## <a id="Run"></a>Executar toda a C# aplicação de consola

Prima **F5** no Visual Studio para criar e executar o concluído C# aplicação de consola no modo de depuração. Deverá ver o resultado seguinte na janela da consola:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Parabéns! Concluiu o tutorial e ter um funcionamento C# aplicação de consola que cria, consultas, atualiza e elimina os recursos do Azure Cosmos DB.  

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](introduction.md).
* Para obter um tutorial de ASP.NET MVC mais complexo, consulte [Tutorial do MVC ASP.NET: Desenvolvimento de aplicação com o Azure Cosmos DB Web](sql-api-dotnet-application.md).
* Para efetuar o dimensionamento e desempenho de teste com o Azure Cosmos DB, consulte [desempenho e dimensionamento testes com o Azure Cosmos DB](performance-testing.md).
* Para saber como monitorizar pedidos, utilização e armazenamento do Azure Cosmos DB, veja [monitorizar contas](monitor-accounts.md).
* Execute consultas no conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).

