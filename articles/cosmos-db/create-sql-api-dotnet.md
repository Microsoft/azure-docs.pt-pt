---
title: Quickstart - Construa uma app de consola .NET para gerir os recursos da API Azure Cosmos DB SQL
description: Aprenda a construir uma aplicação de consola .NET para gerir os recursos da conta Azure Cosmos DB SQL API neste arranque rápido.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 0981ed30c6bcd9d4246ce1eb047aa66168e3884a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240416"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Quickstart: Construa uma app de consola .NET para gerir os recursos da API Azure Cosmos DB SQL

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Inicie-se com a biblioteca de clientes Azure Cosmos DB SQL API para .NET. Siga os passos neste doc para instalar o pacote .NET, construir uma app e experimentar o código de exemplo para operações crud básicas nos dados armazenados em Azure Cosmos DB. 

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode utilizar o Azure Cosmos DB para criar e consultar rapidamente as bases de dados de chaves/valor, documentos e gráficos. Utilize a biblioteca de clientes Azure Cosmos DB SQL API para.NET para:

* Crie uma base de dados Azure Cosmos e um recipiente
* Adicione dados da amostra ao recipiente
* Consultar os dados 
* Apagar a base de dados

[Documentação de referência da API](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | Pacote fonte de código | da [Biblioteca](https://github.com/Azure/azure-cosmos-dotnet-v3) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/) ou pode [Experimentar O Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. 
* O [Núcleo .NET 2.1 SDK ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da criação de uma conta Azure Cosmos e da criação de um projeto que utiliza a biblioteca de clientes Azure Cosmos DB SQL API para a gestão de recursos. O código de exemplo descrito neste artigo cria uma base de dados `FamilyDatabase` e membros da família (cada membro da família é um item) dentro dessa base de dados. Cada membro da família tem propriedades como `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. A propriedade `LastName` é usada como chave de partição para o recipiente. 

### <a id="create-account"></a>Criar uma conta Azure Cosmos

Se utilizar o [Try Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) uma opção gratuita para criar uma conta Azure Cosmos, tem de criar uma conta Azure Cosmos DB do tipo **SQL API**. Já foi criada uma conta de teste Azure Cosmos DB para si. Não tem de criar a conta explicitamente, para que possa saltar esta secção e passar para a secção seguinte.

Se tiver a sua própria subscrição Azure ou tiver criado uma subscrição gratuitamente, deverá criar explicitamente uma conta Azure Cosmos. O código seguinte criará uma conta Azure Cosmos com consistência da sessão. A conta é replicada em `South Central US` e `North Central US`.  

Você pode usar Azure Cloud Shell para criar a conta Azure Cosmos. A Azure Cloud Shell é uma concha interativa, autenticada e acessível ao navegador para gerir os recursos do Azure. Proporciona a flexibilidade de escolher a experiência da concha que melhor se adequa à forma como trabalha, seja a Bash ou a PowerShell. Para este arranque rápido, escolha o modo **Bash.** A Azure Cloud Shell também requer uma conta de armazenamento, podecriar uma quando solicitada.

Selecione o botão **Try It** ao lado do código seguinte, escolha o modo **Bash** selecione criar uma conta de **armazenamento** e iniciar sessão na Cloud Shell. Próxima cópia e cole o seguinte código à casca de nuvem Azure e execute-o. O nome da conta Azure Cosmos deve ser globalmente único, certifique-se de atualizar o valor `mysqlapicosmosdb` antes de executar o comando.

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

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

A criação da conta Azure Cosmos demora algum tempo, uma vez que a operação é bem sucedida, pode ver a saída de confirmação. Depois do comando terminar com sucesso, assine no [portal Azure](https://portal.azure.com/) e verifique se a conta Azure Cosmos com o nome especificado existe. Pode fechar a janela Azure Cloud Shell depois de o recurso ser criado. 

### <a id="create-dotnet-core-app"></a>Criar uma nova aplicação .NET

Crie uma nova aplicação .NET no seu editor ou IDE preferido. Abra o pedido de comando do Windows ou uma janela terminal a partir do seu computador local. Executará todos os comandos nas próximas secções a partir do pedido de comando ou terminal.  Execute o novo comando do dotnet seguinte para criar uma nova app com o nome `todo`. O parâmetro --langVersion define a propriedade LangVersion no ficheiro de projeto criado.

```console
dotnet new console --langVersion 7.1 -n todo
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
cd todo
dotnet build
```

A saída esperada da construção deve ser algo assim:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Instale o pacote Azure Cosmos DB

Ainda no diretório de aplicações, instale a biblioteca de clientes Azure Cosmos DB para .NET Core utilizando o comando de pacote de adição de dotnet.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copie as credenciais da sua conta Azure Cosmos do portal Azure

A aplicação da amostra precisa de autenticar na sua conta Azure Cosmos. Para autenticar, deverá passar as credenciais da conta Azure Cosmos para a aplicação. Obtenha as credenciais da sua conta Azure Cosmos seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para a sua conta Azure Cosmos.

1. Abra o painel **de Teclas** e copie a CHAVE **URI** e **PRIMÁRIA** da sua conta. Você adicionará os valores URI e chaves a uma variável ambiental no próximo passo.

### <a name="set-the-environment-variables"></a>Definir as variáveis ambientais

Depois de ter copiado a CHAVE **URI** e **PRIMÁRIA** da sua conta, guarde-as para uma nova variável ambiental na máquina local que executa a aplicação. Para definir a variável ambiental, abra uma janela da consola e execute o seguinte comando. Certifique-se de substituir os valores `<Your_Azure_Cosmos_account_URI>` e `<Your_Azure_Cosmos_account_PRIMARY_KEY>`.

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

**MacOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a id="object-model"></a>Modelo de objeto

Antes de começar a construir a aplicação, vamos olhar para a hierarquia de recursos em Azure Cosmos DB e o modelo de objeto usado para criar e aceder a esses recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta Azure Cosmos 
* Bases de Dados 
* Contentores 
* Itens

Para saber mais sobre a hierarquia de diferentes entidades, consulte o trabalho com bases de [dados, contentores e itens no artigo da Azure Cosmos DB.](databases-containers-items.md) Utilizará as seguintes classes .NET para interagir com estes recursos:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) - Esta classe fornece uma representação lógica do lado do cliente para o serviço Azure Cosmos DB. O objeto do cliente é usado para configurar e executar pedidos contra o serviço.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) - Este método cria (se não existe) ou obtém (se já existe) um recurso de base de dados como uma operação assíncrona. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)- Este método cria (se não existe) ou obtém (se já existe) um recipiente como uma operação assíncrona. Pode verificar o código de estado a partir da resposta para determinar se o recipiente foi recém-criado (201) ou se um contentor existente foi devolvido (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) - Este método cria um item dentro do recipiente. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) - Este método cria um item dentro do recipiente se já não existir ou substituir o item se já existir. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) - Este método cria uma consulta para itens sob um recipiente numa base de dados Azure Cosmos usando uma declaração SQL com valores parametrizados. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) - Elimina a base de dados especificada da sua conta Azure Cosmos. `DeleteAsync` método apenas elimina a base de dados. A eliminação da `Cosmosclient` instância deve acontecer separadamente (o que acontece no método DeleteDatabaseAndCleanupAsync. 

 ## <a id="code-examples"></a>Exemplos de código

O código de amostra descrito neste artigo cria uma base de dados familiar em Azure Cosmos DB. A base de dados familiar contém detalhes familiares como nome, endereço, localização, pais, crianças e animais de estimação associados. Antes de povoar os dados na sua conta Azure Cosmos, defina as propriedades de um item familiar. Crie uma nova classe chamada `Family.cs` ao nível raiz da sua aplicação de amostra e adicione-lhe o seguinte código:

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

### <a name="add-the-using-directives--define-the-client-object"></a>Adicione as diretivas de utilização e defina o objeto cliente

A partir do diretório do projeto, abra o ficheiro `Program.cs` no seu editor e adicione as seguintes diretivas utilizando as seguintes diretivas no topo da sua aplicação:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Ao ficheiro **Program.cs,** adicione código para ler as variáveis ambientais que definiu no passo anterior. Defina o `CosmosClient`, `Database`, e os objetos `Container`. Em seguida, adicione código ao método principal que chama o método `GetStartedDemoAsync` onde gere os recursos da conta Azure Cosmos. 

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

Defina o método `CreateDatabaseAsync` dentro da classe `program.cs`. Este método cria a `FamilyDatabase` se já não existir.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Criar um contentor

Defina o método `CreateContainerAsync` dentro da classe `program.cs`. Este método cria a `FamilyContainer` se já não existir. 

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

Crie um item familiar adicionando o método `AddItemsToContainerAsync` com o seguinte código. Pode utilizar os métodos `CreateItemAsync` ou `UpsertItemAsync` para criar um item:

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

Depois de inserir um item, pode fazer uma consulta para obter os detalhes da família "Andersen". O código seguinte mostra como executar a consulta utilizando a consulta SQL diretamente. A consulta da SQL para obter os detalhes da família "Anderson" é: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Defina o método `QueryItemsAsync` dentro da classe `program.cs` e adicione-lhe o seguinte código:


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

### <a name="delete-the-database"></a>Apagar a base de dados 

Por fim, pode eliminar a base de dados adicionando o método `DeleteDatabaseAndCleanupAsync` com o seguinte código:

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

### <a name="execute-the-crud-operations"></a>Executar as operações do CRUD

Depois de definir todos os métodos necessários, execute-os com o método `GetStartedDemoAsync`. O método `DeleteDatabaseAndCleanupAsync` comentado neste código porque não verá quaisquer recursos se esse método for executado. Pode descocomentá-lo depois de validar que os seus recursos Azure Cosmos DB foram criados no portal Azure. 

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

Depois de adicionar todos os métodos necessários, guarde o ficheiro `Program.cs`. 

## <a name="run-the-code"></a>Executar o código

Em seguida, construa e execute a aplicação para criar os recursos Do BD Azure Cosmos. Certifique-se de abrir uma nova janela de solicitação de comando, não use a mesma instância que usou para definir as variáveis ambientais. Porque as variáveis ambientais não estão definidas na janela aberta atual. Terá de abrir um novo pedido de comando para ver as atualizações. 

```console
dotnet build
```

```console
dotnet run
```

A seguinte saída é gerada quando executa a aplicação. Também pode entrar no portal Azure e validar que os recursos são criados:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Pode validar que os dados são criados através da assinatura no portal Azure e ver os itens necessários na sua conta Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover a conta Azure Cosmos e o grupo de recursos correspondente. O seguinte comando mostra como eliminar o grupo de recursos utilizando o ClI Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos, criar uma base de dados e um recipiente usando uma aplicação .NET Core. Pode agora importar dados adicionais para a sua conta Azure Cosmos com as instruções int int no seguinte artigo. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
