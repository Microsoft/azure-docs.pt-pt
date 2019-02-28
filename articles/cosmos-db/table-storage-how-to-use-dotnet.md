---
title: Introdução ao Armazenamento de tabelas do Azure e à API de Tabela do Azure Cosmos DB com .NET
description: Armazene dados estruturados na cloud com o armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 08/17/2018
ms.openlocfilehash: 57ed02463555ce9e958aedd9c2b317f7a167567b
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985891"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-net"></a>Introdução ao armazenamento de Tabelas do Azure e à API de Tabelas do Azure Cosmos DB com .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Pode utilizar o armazenamento de Tabelas do Azure e a API de Tabelas do Azure Cosmos DB para armazenar dados NoSQL estruturados na cloud, o que proporciona um arquivo de atributos/chaves com um design sem esquemas. Uma vez que o armazenamento de Tabelas e a API de Tabelas do Azure Cosmos DB não têm esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso aos dados do armazenamento de Tabelas e à API de Tabelas do Azure Cosmos DB é rápido e rentável para muitos tipos de aplicações e, normalmente, é mais económico do que o SQL tradicional para volumes de dados semelhantes.

Pode utilizar o armazenamento de Tabelas ou a API de Tabelas do Azure Cosmos DB para armazenar conjuntos de dados flexíveis, como os dados do utilizador para aplicações Web, livros de endereços, informações do dispositivo ou outros tipos de metadados necessários para o seu serviço. Pode armazenar quantas entidades quiser numa tabela, sendo que uma conta de armazenamento ou uma conta da API de Tabelas pode conter qualquer número de tabelas, até ao limite de capacidade da conta de armazenamento ou da conta da API de Tabelas.

### <a name="about-this-sample"></a>Sobre este exemplo
Este exemplo mostra-lhe como utilizar a [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) nos cenários comuns do armazenamento de Tabelas do Azure e da API de Tabelas. O nome do pacote indica que é para ser utilizado com o Azure Cosmos DB, mas o mesmo funciona quer com a API de Tabelas do Azure Cosmos DB, quer com o armazenamento de Tabelas do Azure; cada serviço tem apenas um ponto final exclusivo. Estes cenários são explorados com exemplos de C# que ilustram como:
* Criar e eliminar tabelas
* Inserir, atualizar e eliminar linhas
* Consulta tabelas

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo com êxito, precisa do seguinte:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Biblioteca Comum do Armazenamento do Azure para .NET (Pré-visualização)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). - Um pacote de pré-visualização obrigatório que é suportado em ambientes de produção. 
* [Biblioteca de Tabela do CosmosDB do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) - Esta biblioteca está atualmente disponível apenas para o .NET Standard, ainda não está disponível para .NET Core.
* [Conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais exemplos
Para obter exemplos adicionais que utilizam o Armazenamento de Tabelas, veja [Introdução ao Armazenamento de Tabelas do Azure do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Pode transferir a aplicação de exemplo e executá-la ou procurar o código no GitHub.

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure
* A forma mais fácil de criar a primeira conta de armazenamento do Azure é com o [portal do Azure](https://portal.azure.com). Para saber mais, veja [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

* Também pode criar uma conta de armazenamento do Azure com o [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), o [CLI do Azure](../storage/common/storage-azure-cli.md) ou a [Biblioteca de Clientes do Fornecedor de Recursos de Armazenamento para .NET](/dotnet/api/microsoft.azure.management.storage).

* Se preferir não criar uma conta de armazenamento neste momento, pode também utilizar o emulador de armazenamento do Azure para executar e testar o seu código num ambiente local. Para obter mais informações, veja [Utilizar o Emulador de Armazenamento do Azure para Programação e Teste](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicação de consola do Windows
No Visual Studio, crie uma nova aplicação de consola do Windows. Os passos seguintes mostram como criar uma aplicação de consola no Visual Studio 2017. Os passos são semelhantes aos de outras versões do Visual Studio.

1. Selecione **Ficheiro** > **Novo** > **Projeto**.
2. Selecione **Instalado** > **Visual C#** > **Ambiente de Trabalho Clássico do Windows**.
3. Selecione **Aplicação da Consola (.NET Framework)**.
4. No campo **Nome**, introduza um nome para a sua aplicação.
5. Selecione **OK**.

Todos os exemplos de código deste exemplo podem ser adicionados ao método `Main()` do ficheiro `Program.cs` da aplicação da consola.

Pode utilizar a Biblioteca de Tabelas do Azure Cosmos DB em qualquer tipo de aplicação .NET, incluindo um serviço cloud do Azure ou aplicação Web, bem como aplicações de ambiente de trabalho e móveis. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

### <a name="install-the-required-nuget-packages"></a>Instale os pacotes NuGet exigidos
Há três pacotes recomendados a que tem de fazer referência no seu projeto para concluir este exemplo:

* [Biblioteca Comum do Armazenamento do Azure para .NET (Pré-visualização)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common). -Utilize uma versão que inferior ou igual a 9.0.0.1 (<= 9.0.0.1).

* [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Este pacote fornece acesso programático a recursos de dados na sua conta de armazenamento de Tabelas do Azure ou conta da API de Tabelas do Azure Cosmos DB. Esta biblioteca está atualmente disponível apenas para o .NET Standard, ainda não está disponível para .NET Core.

Para obter os pacotes NuGet, siga estes passos:

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.
2. Procure online por "Microsoft.Azure.Storage.Common", escolha a versão <= 9.0.0.1 e selecione **Instalar** para instalar a Biblioteca Comum do Armazenamento do Azure para .NET (Pré-visualização) e as respetivas dependências. Certifique-se de que a caixa **Incluir pré-lançamento** está marcada, uma vez que se trata de um pacote de pré-visualização.
3. Procure online por "Microsoft.Azure.CosmosDB.Table" e selecione **Instalar** para instalar a Biblioteca de Tabelas do Microsoft Azure Cosmos DB.

> [!NOTE]
> As dependências ODataLib na Biblioteca Comum de Armazenamento para .NET são resolvidas pelos pacotes ODataLib disponíveis no NuGet e não pelo WCF Data Services. As bibliotecas ODataLib podem ser transferidas diretamente ou referenciadas pelo seu projeto de código através do NuGet. Os pacotes ODataLib específicos utilizados da Biblioteca de Clientes de Armazenamento são [OData](https://nuget.org/packages/Microsoft.Data.OData/), [Edm](https://nuget.org/packages/Microsoft.Data.Edm/) e [Spatial](https://nuget.org/packages/System.Spatial/). Embora estas bibliotecas sejam utilizadas pelas classes do armazenamento de Tabelas do Azure, são dependências necessárias para a programação com a Biblioteca Comum de Armazenamento.
> 
> 

> [!TIP]
> Os programadores que já conhecem o armazenamento de Tabelas do Azure poderão, no passado, ter utilizado o pacote [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/). Recomenda-se que todas as novas aplicações de tabela utilizem a [Biblioteca Comum do Armazenamento do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common) e a [Biblioteca de Tabelas do Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). No entanto, o pacote WindowsAzure.Storage continua a ser suportado. Se utilizar a biblioteca WindowsAzure.Storage, utilize instruções para incluir Microsoft.WindowsAzure.Storage.Table na sua biblioteca.
>
>

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino
Tem três opções de ambiente para executar os exemplos neste guia:

* Pode executar o código numa conta de armazenamento do Azure na nuvem. 
* Pode executar o código numa conta do Azure Cosmos DB na cloud.
* Pode executar o código no emulador de armazenamento do Azure. O emulador de armazenamento é um ambiente local que emula uma conta de armazenamento do Azure na nuvem. O emulador é uma opção gratuita para testar e depurar o seu código enquanto a aplicação está em desenvolvimento. O emulador utiliza uma conta e chave bem conhecidas. Para obter mais informações, veja [Utilizar o emulador de armazenamento do Azure para programação e teste](../storage/common/storage-use-emulator.md).

Se estiver a filtrar uma conta de armazenamento na cloud, copie a chave de acesso primária para a sua conta de armazenamento a partir do portal do Azure. Para obter mais informações, veja [Chaves de acesso da conta de armazenamento](../storage/common/storage-account-manage.md#access-keys).

> [!NOTE]
> Pode filtrar o emulador de armazenamento para evitar incorrer em custos associados ao Storage do Azure. No entanto, se optar por filtrar uma conta de armazenamento do Azure na cloud, os custos para realizar este exemplo serão negligenciáveis.
> 
> 

Se estiver a filtrar uma conta do Azure Cosmos DB, copie a chave de acesso primária para a sua conta da API de Tabelas a partir do portal do Azure. Para obter mais informações, veja [Atualizar a cadeia de ligação](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
A Biblioteca Comum do Armazenamento do Azure para .NET suporta a utilização de uma cadeia de ligação de armazenamento para configurar pontos finais e credenciais para aceder aos serviços de armazenamento. A melhor forma para manter a cadeia de ligação de armazenamento é num ficheiro de configuração. 

Para obter mais informações sobre as cadeias de ligação, veja [Configurar uma cadeia de ligação para o Armazenamento do Azure](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> A chave da conta é semelhante à palavra-passe de raiz da sua conta de armazenamento. Tenha sempre o cuidado de proteger a chave da conta de armazenamento. Evite distribui-la a outros utilizadores, pré-programá-la ou guardá-la num ficheiro de texto simples que seja acessível a outras pessoas. Regenere a sua chave através do portal do Azure se considerar que poderá ter sido comprometida.
> 

Para configurar a cadeia de ligação, abra o ficheiro `app.config` a partir do Explorador de Soluções no Visual Studio. Adicione o conteúdo do elemento `<appSettings>` mostrado abaixo. Substitua `account-name` pelo nome da sua conta e `account-key` pela chave de acesso da conta:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Por exemplo, se estiver a utilizar uma conta do Armazenamento do Azure, as suas definições de configuração serão semelhantes a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>" />
```

Se estiver a utilizar uma conta do Azure Cosmos DB, as suas definições de configuração serão semelhantes a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=<account-key>;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Para filtrar o emulador de armazenamento, pode utilizar um atalho que mapeia para o nome de conta e chave bem conhecidas. Nesse caso, a definição da cadeia de ligação é:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Adicionar com diretivas
Adicione as seguintes diretivas de **utilização** à parte superior do ficheiro `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Criar o cliente do serviço Tabela
A classe [CloudTableClient][dotnet_CloudTableClient] permite-lhe obter tabelas e entidades armazenadas no Armazenamento de tabelas. Eis uma forma de criar o Cliente do serviço de tabelas:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Agora, está pronto para escrever código que lê dados de e escreve dados para o Table Storage.

## <a name="create-a-table"></a>Criar uma tabela
Este exemplo mostra como criar uma tabela se ainda não existir:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
As entidades mapeiam para objetos C# através de uma classe personalizada derivada de [TableEntity][dotnet_TableEntity]. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código seguinte define uma classe de entidade que utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam-na de forma exclusiva na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes, mas a utilização de várias chaves de partição permite uma maior escalabilidade de operações simultâneas. As entidades a serem armazenadas em tabelas têm de ter um tipo suportado, por exemplo derivadas da classe [TableEntity][dotnet_TableEntity]. As propriedades de entidade que pretende armazenar numa tabela têm de ser propriedades públicas do tipo e suportar a introdução e a definição de valores. Além disso, o tipo de entidade *tem de* expor um construtor sem parâmetros.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

As operações de tabela que envolvem entidades são efetuadas através do objeto [CloudTable][dotnet_CloudTable] que criou anteriormente na secção "Criar uma tabela". A operação a realizar é representada por um objeto [TableOperation][dotnet_TableOperation]. O exemplo de código seguinte mostra a criação do objeto [CloudTable][dotnet_CloudTable] e, em seguida, um objeto **CustomerEntity**. Para preparar a operação, é criado um objeto [TableOperation][dotnet_TableOperation] para inserir a entidade de cliente na tabela. Por fim, a operação é executada chamando [CloudTable][dotnet_CloudTable].[Executar][dotnet_CloudTable_Execute].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Pode inserir um lote de entidades numa tabela numa operação de escrita. Algumas outras notas sobre operações em lote:

* Pode efetuar atualizações, eliminações e inserções na mesma operação em lote única.
* Uma única operação em lote pode incluir até 100 entidades.
* Todas as entidades numa única operação em lote têm de ter a mesma chave de partição.
* Embora seja possível executar uma consulta como uma operação em lote, tem de ser a única operação no lote.

O exemplo de código seguinte cria dois objetos de entidade e adiciona cada um deles a [TableBatchOperation][dotnet_TableBatchOperation] através do método [Inserir][dotnet_TableBatchOperation_Insert]. Em seguida, chama-se [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] para executar a operação.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição
Para consultar uma tabela para todas as entidades numa partição, utilize um objeto [TableQuery][dotnet_TableQuery]. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Obter um intervalo de entidades numa partição
Se não pretender consultar todas as entidades de uma partição, pode especificar um intervalo através da combinação do filtro da chave de partição com um filtro da chave da fila. O exemplo de código seguinte utiliza dois filtros para obter todas as entidades na partição “Santos”, em que a chave da fila (nome próprio) começa com uma letra anterior ao “E” do alfabeto e, em seguida, imprime os resultados da consulta.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza [TableOperation][dotnet_TableOperation] para especificar o cliente “Bernardo Santos”. Este método devolve apenas uma entidade em vez de uma coleção e o valor devolvido em [TableResult.Result][dotnet_TableResult].[Resultado][dotnet_TableResult_Result] é um objeto **CustomerEntity**. Especificar as chaves de partição e da fila numa consulta é a forma mais rápida de obter uma única entidade a partir do serviço Tabela.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Substituir uma entidade
Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, guarde as alterações novamente no serviço Tabela. O código seguinte altera o número de telefone de um cliente existente. Em vez de chamar [Inserir][dotnet_TableOperation_Insert], este código utiliza [Substituir][dotnet_TableOperation_Replace]. [Substituir][dotnet_TableOperation_Replace] faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi obtida, caso em que a operação falhará. Esta falha é para impedir que a sua aplicação substitua inadvertidamente uma alteração efetuada entre a obtenção e a atualização por outro componente da aplicação. O processamento adequado desta falha é para obter a entidade novamente, efetuar as alterações (se ainda forem válidas) e, em seguida, executar outra operação de [Substituição][dotnet_TableOperation_Replace]. A secção seguinte irá mostrar como substituir este comportamento.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade
As operações de [Substituição][dotnet_TableOperation_Replace] irão falhar se a entidade tiver sido alterada desde que foi obtida do servidor. Além disso, tem de obter primeiro a entidade do servidor para que a operação de [Substituição][dotnet_TableOperation_Replace] seja concluída com êxito. Por vezes, no entanto, não sabe se a entidade existe no servidor e os valores atuais armazenados na mesma são irrelevantes. A atualização deve substituí-los a todos. Para tal, utilizaria uma operação [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Esta operação insere a entidade se esta não existir ou substitui-a se existir, independentemente de quando foi efetuada a última atualização.

No seguinte exemplo de código, uma entidade de cliente para "Pedro Silva" é criada e inserida na tabela "pessoas". Em seguida, utilizamos a operação [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] para guardar uma entidade com a mesma chave de partição (Silva) e a chave de linha (Pedro) para o servidor, desta vez com um valor diferente para a propriedade PhoneNumber. Uma vez que utilizamos [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], todos os respetivos valores de propriedade são substituídos. No entanto, se uma entidade "Pedro Silva" já não existir na tabela, este seria inserido.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade
Uma consulta de tabela pode obter apenas algumas propriedades de uma entidade em vez de todas as propriedades de entidade. Esta técnica, denominada projeção, reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades grandes. A consulta no seguinte código devolve apenas os endereços de e-mail de entidades na tabela. Isto é feito utilizando uma consulta de [DynamicTableEntity][dotnet_DynamicTableEntity] e também [EntityResolver][dotnet_EntityResolver]. A projeção não é suportada pelo emulador de armazenamento, pelo que este código é executado apenas quando estiver a utilizar uma conta do serviço Tabela.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode facilmente eliminar uma entidade depois de a ter obtido através do mesmo padrão mostrado para atualizar uma entidade. O código seguinte obtém e elimina uma entidade de cliente.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Eliminar uma tabela
Finalmente, o seguinte exemplo de código elimina uma tabela a partir de uma conta do Storage. Uma tabela que foi eliminada estará indisponível para ser recriada durante um determinado período de tempo após a eliminação.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Obter entidades em páginas no modo assíncrono
Se estiver a ler um grande número de entidades e pretende processar/apresentar entidades à medida que são obtidas em vez de aguardar que todas regressem, pode obter entidades utilizando uma consulta segmentada. Este exemplo mostra como devolver resultados nas páginas utilizando o padrão Async-Await para que a execução não esteja bloqueada enquanto está a aguardar pela devolução de um grande conjunto de resultados. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Programação assíncrona com Async-Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do Table Storage, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas:

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* Ver mais exemplos do Armazenamento de Tabelas em [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:
* [Referência da Biblioteca de Clientes do Storage para o .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Referência da API REST](https://msdn.microsoft.com/library/azure/dd179355)
* Saiba como simplificar o código de escrita para trabalhar com o Storage do Azure utilizando o [SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.
* [Introdução ao Blob Storage do Azure utilizando o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
* [Ligar à Base de Dados SQL com o .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) para armazenar dados relacionais.

[Download and install the Azure SDK for .NET]: /develop/net/

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
