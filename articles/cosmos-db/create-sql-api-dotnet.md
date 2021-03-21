---
title: Quickstart - Construa uma aplicação de consola .NET para gerir os recursos API API da Azure Cosmos DB SQL
description: Saiba como construir uma aplicação de consola .NET para gerir os recursos da conta AZure Cosmos DL API neste arranque rápido.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/21/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 4c552e6ac195555990cdbbab44f16be32b7930c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425325"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Quickstart: Construa uma aplicação de consola .NET para gerir os recursos API API da Azure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK v4 de Java](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Começa com a biblioteca de clientes Azure Cosmos DB SQL API para .NET. Siga os passos neste doc para instalar o pacote .NET, construir uma aplicação e experimentar o código de exemplo para operações básicas da CRUD nos dados armazenados no Azure Cosmos DB. 

Azure Cosmos DB é a base de dados NoSQL rápida da Microsoft com APIs abertos para qualquer escala. Pode utilizar a Azure Cosmos DB para criar e consultar rapidamente bases de dados de chaves/valor, documentos e gráficos. Utilize a biblioteca de clientes Azure Cosmos DB SQL API para .NET para:

* Criar uma base de dados Azure Cosmos e um recipiente
* Adicione dados de amostra ao recipiente
* Consultar os dados 
* Eliminar a base de dados

[Documentação de](/dotnet/api/microsoft.azure.cosmos)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-cosmos-dotnet-v3)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscription - [crie um gratuitamente](https://azure.microsoft.com/free/) ou pode [experimentar gratuitamente a Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. 
* O [Núcleo .NET 2.1 SDK ou mais tarde](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da criação de uma conta Azure Cosmos e da criação de um projeto que utiliza a biblioteca de clientes API API AZure Cosmos DB SQL para .NET para gerir recursos. O código de exemplo descrito neste artigo cria uma `FamilyDatabase` base de dados e membros da família (cada membro da família é um item) dentro dessa base de dados. Cada membro da família tem propriedades `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` como. A `LastName` propriedade é usada como chave de partição para o recipiente. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Criar uma conta do Azure Cosmos

Se utilizar o [Try Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) opção gratuita para criar uma conta Azure Cosmos, deve criar uma conta DB Azure Cosmos do tipo **SQL API**. Uma conta de teste DB da Azure Cosmos já foi criada para si. Não precisa de criar a conta explicitamente, para que possa saltar esta secção e passar para a secção seguinte.

Se tiver a sua própria subscrição Azure ou tiver uma subscrição gratuita, deverá criar uma conta Azure Cosmos explicitamente. O seguinte código criará uma conta Azure Cosmos com consistência da sessão. A conta é replicada em `South Central US` e `North Central US` .  

Pode utilizar a Azure Cloud Shell para criar a conta Azure Cosmos. O Azure Cloud Shell é uma shell interativa, autenticada e compatível com browsers para gerir recursos do Azure. Dá a flexibilidade de escolher a experiência de shell mais adequada ao seu método de trabalho, quer seja baseada em Bash ou no PowerShell. Para este arranque rápido, escolha o modo **Bash.** AZure Cloud Shell também requer uma conta de armazenamento, pode criar uma quando solicitada.

Selecione o botão **Try It** ao lado do seguinte código, escolha o modo **Bash** **selecione criar uma conta de armazenamento** e iniciar sessão na Cloud Shell. Em seguida, copie e cole o seguinte código para Azure Cloud Shell e execute-o. O nome da conta Azure Cosmos deve ser globalmente único, certifique-se de atualizar o `mysqlapicosmosdb` valor antes de executar o comando.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

A criação da conta Azure Cosmos demora algum tempo, uma vez que a operação é bem sucedida, você pode ver a saída de confirmação. Após o comando ter concluído com sucesso, inscreva-se no [portal Azure](https://portal.azure.com/) e verifique se existe a conta Azure Cosmos com o nome especificado. Pode fechar a janela Azure Cloud Shell após a criação do recurso. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Criar uma nova aplicação .NET

Crie uma nova aplicação .NET no seu editor preferido ou IDE. Abra o pedido de comando do Windows ou uma janela do terminal a partir do computador local. Executará todos os comandos nas próximas secções a partir do comando ou terminal.  Executar o seguinte comando dotnet novo para criar uma nova app com o nome `todo` . O parâmetro langVersion define a propriedade LangVersion no ficheiro do projeto criado.

```console
dotnet new console --langVersion 7.1 -n todo
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
cd todo
dotnet build
```

A produção esperada da construção deve ser algo assim:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instale o pacote DB Azure Cosmos

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes Azure Cosmos DB para .NET Core utilizando o comando do pacote de adicionar dotnet.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copie as suas credenciais de conta Azure Cosmos do portal Azure

A aplicação da amostra precisa de autenticar na sua conta Azure Cosmos. Para autenticar, deve passar as credenciais de conta Azure Cosmos para a aplicação. Obtenha as suas credenciais de conta Azure Cosmos seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para a sua conta Azure Cosmos.

1. Abra o painel **de chaves** e copie o **URI** e **a CHAVE PRINCIPAL** da sua conta. Irá adicionar o URI e os valores das teclas a uma variável ambiental no passo seguinte.

### <a name="set-the-environment-variables"></a>Definir as variáveis ambientais

Depois de copiar o **URI** e a **CHAVE PRIMÁRIA** da sua conta, guarde-os para uma nova variável ambiental na máquina local que executa a aplicação. Para definir a variável ambiente, abra uma janela da consola e execute o seguinte comando. Certifique-se de que substitui `<Your_Azure_Cosmos_account_URI>` e `<Your_Azure_Cosmos_account_PRIMARY_KEY>` valoriza.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**macOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Modelo de objeto

Antes de começar a construir a aplicação, vamos olhar para a hierarquia de recursos em Azure Cosmos DB e o modelo de objeto usado para criar e aceder a estes recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta Azure Cosmos 
* Bases de Dados 
* Contentores 
* Itens

Para saber mais sobre a hierarquia de diferentes entidades, consulte o [trabalho com bases de dados, contentores e itens no artigo da Azure Cosmos DB.](account-databases-containers-items.md) Utilizará as seguintes classes .NET para interagir com estes recursos:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) - Esta classe proporciona uma representação lógica do lado do cliente para o serviço DB Azure Cosmos. O objeto cliente é usado para configurar e executar pedidos contra o serviço.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) - Este método cria (se não existir) ou obtém (se já existe) um recurso de base de dados como uma operação assíncrono. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync)- - Este método cria (se não existir) ou obtém (se já existe) um recipiente como uma operação assíncrona. Pode verificar o código de estado da resposta para determinar se o recipiente foi recém-criado (201) ou se foi devolvido um contentor existente (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) - Este método cria um item dentro do recipiente. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) - Este método cria um item dentro do recipiente se já não existir ou substituir o item se já existir. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator) - Este método cria uma consulta para itens debaixo de um recipiente numa base de dados do Azure Cosmos utilizando uma declaração SQL com valores parametrizados. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) - Elimina a base de dados especificada da sua conta Azure Cosmos. `DeleteAsync` método apenas elimina a base de dados. A eliminação do `Cosmosclient` caso deve ocorrer separadamente (o que acontece no método DeleteDatabaseAndCleanUpAsync. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Exemplos de código

O código de amostra descrito neste artigo cria uma base de dados familiar em Azure Cosmos DB. A base de dados da família contém detalhes familiares como nome, endereço, localização, pais, crianças e animais de estimação associados. Antes de povoar os dados para a sua conta Azure Cosmos, defina as propriedades de um item familiar. Crie uma nova classe nomeada `Family.cs` ao nível da raiz da sua aplicação de amostra e adicione-lhe o seguinte código:

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
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
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

### <a name="add-the-using-directives--define-the-client-object"></a>Adicione as diretivas de utilização & definir o objeto cliente

A partir do diretório do projeto, abra o `Program.cs` ficheiro no seu editor e adicione as seguintes diretivas no topo da sua aplicação:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Ao ficheiro **.cs Programa,** adicione código para ler as variáveis ambientais que definiu no passo anterior. Defina `CosmosClient` `Database` o, e os `Container` objetos. Em seguida, adicione código ao método principal que chama o `GetStartedDemoAsync` método onde gere os recursos da conta Azure Cosmos. 

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

Defina o `CreateDatabaseAsync` método dentro da `program.cs` classe. Este método cria o `FamilyDatabase` se já não existe.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Criar um contentor

Defina o `CreateContainerAsync` método dentro da `program.cs` classe. Este método cria o `FamilyContainer` se já não existe. 

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

Crie um item familiar adicionando o `AddItemsToContainerAsync` método com o seguinte código. Pode utilizar os `CreateItemAsync` métodos ou `UpsertItemAsync` métodos para criar um item:

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
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, você pode executar uma consulta para obter os detalhes da família "Andersen". O código que se segue mostra como executar a consulta utilizando a consulta SQL diretamente. A consulta do SQL para obter os detalhes da família "Anderson" é: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Defina o `QueryItemsAsync` método dentro da classe e `program.cs` adicione-lhe o seguinte código:


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

### <a name="delete-the-database"></a>Eliminar a base de dados 

Finalmente pode eliminar a base de dados adicionando o `DeleteDatabaseAndCleanupAsync` método com o seguinte código:

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

Depois de definir todos os métodos necessários, execute-os com o `GetStartedDemoAsync` método. O `DeleteDatabaseAndCleanupAsync` método comentado neste código porque não verá quaisquer recursos se esse método for executado. Pode descomprê-lo depois de validar que os seus recursos DB Azure Cosmos foram criados no portal Azure. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Depois de adicionar todos os métodos necessários, guarde o `Program.cs` ficheiro. 

## <a name="run-the-code"></a>Executar o código

Em seguida, construa e execute a aplicação para criar os recursos DB Azure Cosmos. Certifique-se de abrir uma nova janela de pedido de comando, não utilize a mesma instância que usou para definir as variáveis ambientais. Porque as variáveis ambientais não estão definidas na janela aberta atual. Terá de abrir um novo pedido de comando para ver as atualizações. 

```console
dotnet build
```

```console
dotnet run
```

A seguinte saída é gerada quando executar a aplicação. Também pode entrar no portal Azure e validar que os recursos são criados:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Pode validar que os dados são criados assinando no portal Azure e ver os itens necessários na sua conta Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover a conta Azure Cosmos e o grupo de recursos correspondente. O seguinte comando mostra como eliminar o grupo de recursos utilizando o Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta Azure Cosmos, criar uma base de dados e um contentor utilizando uma aplicação .NET Core. Pode agora importar dados adicionais para a sua conta Azure Cosmos com as instruções int o seguinte artigo. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).