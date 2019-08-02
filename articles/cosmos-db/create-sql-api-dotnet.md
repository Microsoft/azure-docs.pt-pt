---
title: Criar um aplicativo de console .NET para gerenciar Azure Cosmos DB recursos da API do SQL
description: Saiba como criar um aplicativo de console .NET para gerenciar Azure Cosmos DB recursos de conta da API do SQL.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: c738b2d44c5faca1ef95b2da8fd1f90a1b3af919
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371026"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Início rápido: Criar um aplicativo de console .NET para gerenciar Azure Cosmos DB recursos da API do SQL

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Introdução à biblioteca de cliente da API do SQL Azure Cosmos DB para .NET. Siga as etapas neste documento para instalar o pacote .NET, criar um aplicativo e experimentar o código de exemplo para operações CRUD básicas nos dados armazenados no Azure Cosmos DB. 

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Você pode usar Azure Cosmos DB para criar e consultar rapidamente bancos de dados de chave/valor, documento e gráfico. Use a biblioteca de cliente da API do SQL do Azure Cosmos DB para .NET para:

* Criar um banco de dados Cosmos do Azure e um contêiner
* Adicionar dados de exemplo ao contêiner
* Consultar os dados 
* Excluir o banco de dados

[](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | [Código](https://github.com/Azure/azure-cosmos-dotnet-v3)[](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) -fonte da biblioteca de documentação de referência de API (NuGet) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie um gratuitamente](https://azure.microsoft.com/free/) ou você pode [tentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, gratuitamente e com compromissos. 
* O [SDK do .NET Core 2,1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela criação de uma conta do Azure Cosmos e pela configuração de um projeto que usa Azure Cosmos DB biblioteca de cliente da API do SQL para .NET para gerenciar recursos. O código de exemplo descrito neste artigo cria um `FamilyDatabase` banco de dados e membros da família (cada membro da família é um item) dentro desse banco de dados. Cada membro da família tem propriedades como `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. A `LastName` propriedade é usada como a chave de partição para o contêiner. 

### <a id="create-account"></a>Criar uma conta do Azure Cosmos

O código a seguir criará uma conta do Azure cosmos com consistência de sessão. A conta é replicada no `South Central US` e `North Central US`no. Selecione o botão **experimentar** e cole o código para executá-lo no Azure cloud Shell. 

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'
accountName='mysqlapicosmosdb' 
databaseName='FamilyDatabase'
containerName='FamilyContainer'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

### <a id="create-dotnet-core-app"></a>Criar um novo aplicativo .NET

Crie um novo aplicativo .NET em seu editor preferido ou IDE. Em uma janela de console, execute o seguinte comando dotnet novo para criar um novo aplicativo com o `todo`nome.

```console
dotnet new console --langVersion 7.1 -n todo
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```console
cd todo
dotnet build
```

A saída esperada da compilação deve ser semelhante a esta:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Instalar o pacote de Azure Cosmos DB

Ainda no diretório do aplicativo, instale a biblioteca de cliente do Azure Cosmos DB para .NET Core usando o comando dotnet adicionar pacote.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copie suas credenciais de conta do Azure Cosmos do portal do Azure

O aplicativo de exemplo precisa se autenticar na sua conta do Azure Cosmos. Para autenticar, você deve passar as credenciais da conta do Azure Cosmos para o aplicativo. Obtenha suas credenciais de conta do Azure Cosmos seguindo estas etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue até sua conta do Azure Cosmos. 

1. Abra o painel **chaves** e copie o **URI** e a **chave primária** da sua conta. Você adicionará os valores de URI e de chaves a uma variável de ambiente na próxima etapa.

### <a name="set-the-environment-variables"></a>Definir as variáveis de ambiente

Depois de ter copiado o **URI** e a **chave primária** da sua conta, salve-os em uma nova variável de ambiente no computador local que executa o aplicativo. Para definir a variável de ambiente, abra uma janela de console e execute o comando a seguir. Certifique-se de `<Your_Azure_Cosmos_account_URI>` substituir `<Your_Azure_Cosmos_account_PRIMARY_KEY>` os valores e.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**MacOS**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a id="object-model"></a>Modelo de objeto

Antes de começar a criar o aplicativo, vamos examinar a hierarquia de recursos em Azure Cosmos DB e o modelo de objeto usado para criar e acessar esses recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta do Azure Cosmos 
* Bases de Dados 
* Contentores 
* Itens

Para saber mais sobre a hierarquia de entidades diferentes, consulte o artigo [trabalhando com bancos de dados, contêineres e itens no Azure Cosmos DB](databases-containers-items.md) . Você usará as seguintes classes .NET para interagir com estes recursos:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) -essa classe fornece uma representação lógica do lado do cliente para o serviço de Azure Cosmos DB. O objeto de cliente é usado para configurar e executar solicitações no serviço.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) -esse método cria (se não existir) ou obtém (se já existe) um recurso de banco de dados como uma operação assíncrona. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)--esse método cria (se ele não existir) ou obtém (se já existir) um contêiner como uma operação assíncrona. Você pode verificar o código de status da resposta para determinar se o contêiner foi criado recentemente (201) ou se um contêiner existente foi retornado (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) -esse método cria um item dentro do contêiner. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) -esse método cria uma consulta para itens em um contêiner em um banco de dados Cosmos do Azure usando uma instrução SQL com valores com parâmetros. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) -exclui o banco de dados especificado de sua conta do Azure Cosmos. `DeleteAsync`o método só exclui o banco de dados. A `Cosmosclient` descartar da instância deve ocorrer separadamente (o que faz no método DeleteDatabaseAndCleanupAsync. 

 ## <a id="code-examples"></a>Exemplos de código

O código de exemplo descrito neste artigo cria um banco de dados de família em Azure Cosmos DB. O banco de dados da família contém detalhes da família, como nome, endereço, local, pais associados, filhos e animais de estimação. Antes de preencher os dados para sua conta do Azure Cosmos, defina as propriedades de um item da família. Crie uma nova classe chamada `Family.cs` no nível raiz do seu aplicativo de exemplo e adicione o seguinte código a ela:

```csharp
using Newtonsoft.Json;

namespace todo
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

### <a name="add-the-using-directives--define-the-client-object"></a>Adicionar as diretivas using & definir o objeto de cliente

No diretório do projeto, abra o `Program.cs` arquivo em seu editor e adicione as seguintes diretivas using na parte superior do seu aplicativo:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Para o `program.cs file`, adicione o código para ler as variáveis de ambiente que você definiu na etapa anterior. Defina os `CosmosClient` `Container` objetos `Database`, e. Em seguida, adicione o código ao método Main que `GetStartedDemoAsync` chama o método no qual você gerencia os recursos da conta do Azure Cosmos. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

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
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Criar uma base de dados 

Defina o `CreateDatabaseAsync` método dentro da `program.cs` classe. Esse método criará `FamilyDatabase` o se ele ainda não existir. 

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Criar um contentor

Defina o `CreateContainerAsync` método dentro da `program.cs` classe. Esse método criará `FamilyContainer` o se ele ainda não existir. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Criar um item

Crie um item de família adicionando o `AddItemsToContainerAsync` método com o seguinte código:

```csharp
private async Task AddItemsToContainerAsync()
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
        IsRegistered = false
 };

try
{
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}

```

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, você pode executar uma consulta para obter os detalhes da família "Andersen". O código a seguir mostra como executar a consulta usando a consulta SQL diretamente. A consulta SQL para obter os detalhes da família "Anderson" é `SELECT * FROM c WHERE c.LastName = 'Andersen'`:. Defina o `QueryItemsAsync` método dentro da `program.cs` classe e adicione o seguinte código a ele:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Excluir o banco de dados 

Por fim, você pode excluir o banco `DeleteDatabaseAndCleanupAsync` de dados adicionando o método com o seguinte código:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Executar as operações CRUD

Depois de definir todos os métodos necessários, execute-os com o `GetStartedDemoAsync` no método. O `DeleteDatabaseAndCleanupAsync` método comentou neste código, pois você não verá nenhum recurso se esse método for executado. Você pode remover os comentários após validar que seus Azure Cosmos DB recursos foram criados no portal do Azure. 

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
    await this.DeleteFamilyItemAsync();
    //await this.DeleteDatabaseAndCleanupAsync();
}
```

Depois de adicionar todos os métodos necessários, salve o `Program.cs` arquivo. 

## <a name="run-the-code"></a>Executar o código

Em seguida, compile e execute o aplicativo para criar os recursos de Azure Cosmos DB. Certifique-se de abrir uma nova janela de prompt de comando, não use a mesma instância que você usou para definir as variáveis de ambiente. Porque as variáveis de ambiente não estão definidas na janela aberta atual. Será necessário abrir um novo prompt de comando para ver as atualizações. 

```console
dotnet build
```

```console
dotnet run
```

A saída a seguir é gerada quando você executa o aplicativo. Você também pode entrar no portal do Azure e validar que os recursos são criados:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Você pode validar que os dados são criados entrando no portal do Azure e ver os itens necessários em sua conta do Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o CLI do Azure ou Azure PowerShell para remover a conta do Azure Cosmos e o grupo de recursos correspondente. O comando a seguir mostra como excluir o grupo de recursos usando o CLI do Azure:

```azurecli
az group delete -g "myResourceGroup" -l "southcentralus"
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu a criar uma conta do Azure Cosmos, criar um banco de dados e um contêiner usando um aplicativo .NET Core. Agora você pode importar dados adicionais para sua conta do Azure cosmos com as instruções int no seguinte artigo. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
