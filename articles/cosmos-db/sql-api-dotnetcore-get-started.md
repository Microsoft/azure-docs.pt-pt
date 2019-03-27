---
title: 'Tutorial: Criar uma aplicação .NET Core para gerir os dados armazenados na conta SQL API do Azure Cosmos DB'
description: Este tutorial cria uma base de dados online e C# aplicação de consola utilizando o SDK .NET Core API de SQL para o Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 52e39b705b8bd0e20c846f065702bcaf7b3a45f1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487283"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: Criar uma aplicação .NET Core para gerir os dados armazenados numa conta SQL API

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (pré-visualização)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (pré-visualização)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como desenvolvedor, pode ter aplicações que utilizam dados de documentos NoSQL. Pode utilizar a conta de API de SQL no Azure Cosmos DB para armazenar e aceder a estes dados de documento. Este tutorial mostra-lhe como criar uma aplicação .NET Core para criar e consultar dados armazenados na conta SQL API do Azure Cosmos DB. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar e ligar a uma conta do Cosmos do Azure
> * Configurar a sua solução Visual Studio
> * Criação de uma base de dados online
> * Criação de uma coleção
> * Criação de documentos JSON
> * Realizar operações de CRUD nos itens, no contentor e na base de dados

Não tem tempo para criar a aplicação? A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Transferir e utilizar gratuitamente [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Se estiver a desenvolver uma aplicação da Plataforma Universal do Windows (UWP), deve utilizar o **Visual Studio 2017 com a versão 15.4** ou superior. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

    * Para MacOS ou Linux, pode desenvolver aplicações .NET Core a partir da linha de comandos ao instalar o [.NET Core SDK](https://www.microsoft.com/net/core#macos) para a plataforma à sua escolha. 

    * Para Windows, pode desenvolver aplicações .NET Core a partir da linha de comandos ao instalar o [.NET Core SDK](https://www.microsoft.com/net/core#windows). 

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Cosmos do Azure

Utilize os seguintes passos para criar uma conta do Cosmos do Azure:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurar a sua solução Visual Studio

1. Abra o **Visual Studio 2017** no seu computador.

2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, selecione **Modelos** > **Visual C#** > **.NET Core** > **Aplicação de Consola (.NET Core)**, atribua ao projeto o nome **DocumentDBGettingStarted** e selecione **OK**.

   ![Captura de ecrã da janela Novo Projeto](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. Na **Explorador de soluções**, clique com botão direito **DocumentDBGettingStarted**.

5. No menu da mesmo, selecione **gerir pacotes NuGet**.

   ![Captura de ecrã do menu de atalho para o projeto](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Sobre o **NuGet** separador, selecione **procurar** na parte superior da janela e escreva **documentdb do azure** na caixa de pesquisa. Certifique-se de que o **incluir pré-lançamento** caixa de verificação.

7. Nos resultados, encontre **Microsoft.Azure.DocumentDB.Core** e selecione **instalar**.

   O ID de pacote da biblioteca é [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Se estiver a filtrar uma versão do .NET Framework (como net461) que não é suportada por este pacote.NET Core NuGet, em seguida, utilize [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB), que suporta todas as versões do .NET Framework a partir do .NET Framework 4.5.

8. Quando pedido, aceite as instalações de pacotes NuGet e o contrato de licença.

Agora que a configuração está concluída, vamos começar a escrever alguns códigos. Encontrará um projeto de código de conclusão deste tutorial [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Ligar a uma conta do Cosmos do Azure

Ligar a uma conta do Cosmos do Azure ao importar as dependências necessárias. Para importar as dependências, adicione o seguinte código no início do ficheiro Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Em seguida, adicione estas duas constantes e seus *cliente* variável por baixo da sua classe pública *programa*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

De seguida, vá para o [portal do Azure](https://portal.azure.com) para obter o seu URI e a chave primária. O URI e a chave primária do Azure Cosmos DB são necessários para que a sua aplicação saiba onde ligar e para que o Azure Cosmos DB confie na ligação da sua aplicação.

No portal do Azure, aceda à sua conta do Cosmos do Azure e, em seguida, selecione **chaves**.

Copie o URI do portal e cole-o em `<your endpoint URI>`, no ficheiro program.cs. Em seguida, copie A CHAVE PRIMÁRIA do portal e cole-a em `<your key>`. Certifique-se de que remove o < and >, mas deixe as aspas no ponto final e na chave.

![Obter chaves do portal do Azure][keys]

Iremos começar a aplicação de introdução pela criação de uma nova instância do **DocumentClient**.

Abaixo do **Main** método, adicione a nova tarefa assíncrona designada **GetStartedDemo**, que irá criar uma instância de seu novo **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Adicione o seguinte código para executar a tarefa assíncrona a partir do seu método **Principal**. O método **Principal** captura as exceções e escreve-as na consola.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Selecione o botão **DocumentDBGettingStarted** para criar e executar a aplicação.

## <a id="CreateDatabase"></a>Criar uma Base de Dados

Antes de adicionar o código para a criação de uma base de dados, adicione um método de programa auxiliar para escrever na consola. Copie e cole o método **WriteToConsoleAndPromptToContinue** por baixo do método **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Criar a sua base de dados do Azure Cosmos DB utilizando a `CreateDatabaseAsync` método da **DocumentClient** classe. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções. Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação de cliente. Esta ação cria uma base de dados designada *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Selecione o botão **DocumentDBGettingStarted** para executar a aplicação.

## <a id="CreateColl"></a>Criar uma coleção

Criar uma coleção com o `CreateDocumentCollectionAsync` método da **DocumentClient** classe. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

> [!WARNING]
> O **CreateDocumentCollectionAsync** cria uma nova coleção com débito reservado, o que causa repercussões sobre os preços. Para obter mais detalhes, vá para o [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação da base de dados. Este código cria uma coleção de documentos com o nome *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Selecione o botão **DocumentDBGettingStarted** para executar a aplicação.

## <a id="CreateDoc"></a>Criar documentos JSON

Criar um documento com o `CreateDocumentAsync` método da **DocumentClient** classe. Os documentos são conteúdos JSON (arbitrários) definidos pelo utilizador. Pode agora inserir um ou mais documentos. 

Em primeiro lugar, crie uma **família** classe que representa os objetos armazenados no Azure Cosmos DB. Também cria **pai**, **subordinado**, **animal de estimação**, e **endereço** subclasses utilizados nos **família**. Os documentos têm de ter uma propriedade de **Id** serializado como **id** no JSON. Crie estas classes ao adicionar as seguintes subclasses internas a seguir ao método **GetStartedDemo**. Copie e cole as classes **Família**, **Principal**, **Subordinado**, **Animal de estimação**, e **Endereço** por baixo do método **WriteToConsoleAndPromptToContinue**.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

Copie e cole o método **CreateFamilyDocumentIfNotExists** por baixo do seu método **CreateDocumentCollectionIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

E insira dois documentos, ou seja um para a Família Andersen e outro para a Família Wakefield. Copie e cole o código a seguir a `// ADD THIS PART TO YOUR CODE` no método **GetStartedDemo**, por baixo da criação da coleção de documentos.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Selecione o botão **DocumentDBGettingStarted** para executar a aplicação.

![Relação hierárquica entre a conta, a base de dados online, a coleção](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB suporta consultas extensas de documentos JSON armazenados em cada coleção. O código de exemplo seguinte mostra várias consultas - utilizando sintaxe SQL do Azure Cosmos DB e LINQ - que pode executar nos documentos que inserimos no passo anterior.

Copie e cole o método **ExecuteSimpleQuery** por baixo do seu método **CreateFamilyDocumentIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação do segundo documento.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Selecione o botão **DocumentDBGettingStarted** para executar a aplicação.

O seguinte diagrama ilustra como a sintaxe de consulta SQL do Azure Cosmos DB é chamada na coleção que criou. A mesma lógica aplicada à consulta LINQ.

![Diagrama que ilustra o âmbito e o significado da consulta utilizada pelo tutorial NoSQL para criar uma aplicação de consola C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

O `FROM` palavra-chave é opcional na consulta porque o Azure Cosmos DB consultas já estão confinadas a uma única coleção. Por conseguinte, "FROM Families f" pode ser trocada por "FROM root r" ou qualquer outra variável de nome escolher. O Azure Cosmos DB infere que o famílias, raiz ou nome da variável que escolheu referencia o conjunto atual por predefinição.

## <a id="ReplaceDocument"></a>Substituir um documento JSON

O Azure Cosmos DB suporta a substituição de documentos JSON.  

Copie e cole o método **ReplaceFamilyDocument** por baixo do seu método **ExecuteSimpleQuery**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da execução da consulta. Depois de substituir o documento, este irá executar a mesma consulta par ver o documento alterado.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Selecione o botão **DocumentDBGettingStarted** para executar a aplicação.

## <a id="DeleteDocument"></a>Eliminar um documento JSON

O Azure Cosmos DB suporta a eliminação de documentos JSON.  

Copie e cole o método **DeleteFamilyDocument** por baixo do seu método **ReplaceFamilyDocument**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da segunda execução de consulta.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Selecione o botão **DocumentDBGettingStarted** para executar a aplicação.

## <a id="DeleteDatabase"></a>Eliminar a base de dados

Eliminar a base de dados criada irá remover a base de dados e todos os recursos subordinados (coleções, documentos, etc.). Copie e cole o seguinte código no seu **GetStartedDemo** método por baixo do documento para eliminar a base de dados e todos os recursos subordinados.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Selecione o botão **DocumentDBGettingStarted** para executar a aplicação.

## <a id="Run"></a>Execute o C# aplicação de consola

Selecione o botão **DocumentDBGettingStarted** no Visual Studio para criar a aplicação no modo de depuração. Deverá ver o resultado da sua aplicação Introdução na janela da consola. O resultado apresentará os resultados das consultas que adicionámos e deverá corresponder ao texto de exemplo abaixo.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Agora concluiu o tutorial e ter um funcionamento C# aplicação de consola.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a conta do Cosmos do Azure e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **eliminar**e, em seguida, confirme o nome do grupo de recursos para eliminar.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como criar uma aplicação .NET Core para gerir os dados armazenados na conta SQL API do Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Criar uma aplicação de consola Java com a conta de API de SQL do Azure Cosmos DB](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
