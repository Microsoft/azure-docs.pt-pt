---
title: Começar com o armazenamento de mesa Azure usando o Visual Studio (ASP.NET)
description: Como começar a usar o armazenamento de mesa Azure num projeto de ASP.NET no Estúdio Visual depois de se ligar a uma conta de armazenamento usando serviços visual studio connected services
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c49df689ae859c93046c19af043aa2001dbb5481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979633"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Inicie-se com armazenamento de mesa Azure e Serviços Conectados de Estúdio Visual (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

O armazenamento da Mesa Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é uma loja de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Este tutorial mostra como escrever ASP.NET código para alguns cenários comuns usando entidades de armazenamento de mesa Azure. Estes cenários incluem a criação de uma tabela, e a adição, consulta e apagando entidades de mesa. 

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento azure](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Solution Explorer,** **controladores**de clique soro direito, e, a partir do menu de contexto, selecione **Add->Controller**.

    ![Adicione um controlador a uma aplicação ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. No diálogo **Add Scaffold,** selecione **Controlador MVC 5 - Vazio**, e selecione **Adicionar**.

    ![Especificar o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. No diálogo **do Controlador adicionar,** nomeie o controlador de *tabelas do*controlador , e selecione **Adicionar**.

    ![Nomeie o controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adicione as seguintes diretivas *utilizando* o `TablesController.cs` ficheiro:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Criar uma classe modelo

Muitos dos exemplos deste artigo usam uma classe derivada de **TableEntity**chamada **CustomerEntity**. Os seguintes passos guiam-no através da declaração desta classe como uma classe modelo:

1. No **Solution Explorer**, **modelos**de clique direito, e, a partir do menu de contexto, selecione **Classe Add->**.

1. No diálogo **Add New Item,** nomeie a classe, **CustomerEntity**.

1. Abra `CustomerEntity.cs` o ficheiro e adicione a seguinte diretiva **utilizando:**

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Modifique a classe de modo a que, quando terminada, a classe seja declarada como no seguinte código. A classe declara uma classe de entidade chamada **CustomerEntity** que usa o primeiro nome do cliente como chave de linha e sobrenome como chave de partição.

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
    }
    ```

## <a name="create-a-table"></a>Criar uma tabela

Os seguintes passos ilustram como criar uma tabela:

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos em [Configurar o ambiente](#set-up-the-development-environment)de desenvolvimento. 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **CreateTable** que devolve um **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **CreateTable,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço de mesa.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que represente uma referência ao nome de mesa desejado. O método **CloudTableClient.GetTableReference** não faz um pedido contra o armazenamento de mesa. A referência é devolvida se a mesa existe ou não. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Ligue para o método **CloudTable.CreateIfNotExists** para criar a tabela se ainda não existir. O método **CloudTable.CreateIfNotExists** retorna **verdadeiro** se a tabela não existir, e é criada com sucesso. Caso contrário, **o falso** é devolvido.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Atualize o **ViewBag** com o nome da tabela.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** **as tabelas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza a **CreateTable** para o nome da visualização e selecione **Adicionar**.

1. Abra `CreateTable.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Executar a aplicação e selecionar tabela **Criar** tabela para ver resultados semelhantes aos seguintes screenshot:
  
    ![Criar tabela](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Como mencionado anteriormente, o método **CloudTable.CreateIfNotExists** retorna **verdadeiro apenas** quando a tabela não existe e é criada. Portanto, se executar a aplicação quando a tabela existe, o método devolve **falso**. Para executar a aplicação várias vezes, deve apagar a tabela antes de executar novamente a aplicação. A eliminação da tabela pode ser feita através do método **CloudTable.Delete.** Também pode eliminar a tabela utilizando o [portal Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

*Entidades* mapeiam objetos C\# utilizando uma classe personalizada derivada da **TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. Nesta secção, você verá como definir uma classe de entidade que usa o primeiro nome do cliente como chave de linha e sobrenome como chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes, mas a utilização de várias chaves de partição permite uma maior escalabilidade de operações simultâneas. Para qualquer imóvel que deva ser armazenado no serviço de mesa, o imóvel deve ser uma propriedade pública de um tipo suportado que exponha tanto valores de fixação como de recuperação.
A classe da entidade *deve* declarar um construtor público sem parâmetros.

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos em [Configurar o ambiente](#set-up-the-development-environment)de desenvolvimento.

1. Abra o ficheiro `TablesController.cs`.

1. Adicione a seguinte diretiva para que `TablesController.cs` o código no ficheiro possa aceder à classe **CustomerEntity:**

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adicione um método chamado **AddEntity** que devolve um **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **AddEntity,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço de mesa.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que represente uma referência à tabela à qual vai adicionar a nova entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instantie e inicialize a classe **CustomerEntity.**

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Crie um objeto **tableOperation** que insira a entidade cliente.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Execute a operação de inserção ligando para o método **CloudTable.Execute.** Pode verificar o resultado da operação inspecionando a propriedade **TableResult.HttpStatusCode.** Um código de estado de 2xx indica que a ação solicitada pelo cliente foi processada com sucesso. Por exemplo, inserções bem sucedidas de novas entidades resultam num código de estado HTTP de 204, o que significa que a operação foi processada com sucesso e o servidor não devolveu qualquer conteúdo.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Atualize o **ViewBag** com o nome da tabela e os resultados do funcionamento da inserção.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** **as tabelas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza AddEntity para o nome da **visualização** e selecione **Adicionar**.

1. Abra `AddEntity.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Executar a aplicação e selecionar **adicionar entidade** para ver resultados semelhantes ao seguinte screenshot:
  
    ![Adicionar entidade](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Pode verificar se a entidade foi adicionada seguindo os passos na secção, [Obtenha uma única entidade](#get-a-single-entity). Também pode utilizar o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas as entidades para as suas tabelas.

## <a name="add-a-batch-of-entities-to-a-table"></a>Adicione um lote de entidades a uma mesa

Além de poder adicionar uma entidade a uma mesa um de [cada vez,](#add-an-entity-to-a-table)também pode adicionar entidades em lote. A adição de entidades em lote reduz o número de viagens de ida e volta entre o seu código e o serviço de mesa Azure. Os seguintes passos ilustram como adicionar várias entidades a uma tabela com uma única operação de inserção:

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos em [Configurar o ambiente](#set-up-the-development-environment)de desenvolvimento.

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **AddEntities** que devolve um **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **AddEntities,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço de mesa.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que represente uma referência à tabela à qual vai adicionar as novas entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instantie alguns objetos do cliente com base na classe modelo **CustomerEntity** apresentado na secção, [Adicione uma entidade a uma tabela](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Arranja um objeto **de TableBatchOperation.**

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Adicione entidades ao objeto de funcionamento de inserção do lote.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Execute a operação de inserção do lote, ligando para o método **CloudTable.ExecuteBatch.**   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. O método **CloudTable.ExecuteBatch** devolve uma lista de objetos **TableResult** onde cada objeto **TableResult** pode ser examinado para determinar o sucesso ou falha de cada operação individual. Para este exemplo, passe a lista para uma vista e deixe a visualização exibir os resultados de cada operação. 
 
    ```csharp
    return View(results);
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** **as tabelas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **AddEntities** para o nome da vista e selecione **Adicionar**.

1. Abra `AddEntities.cshtml`e modifique-o para que se pareça com o seguinte.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Executar a aplicação e selecionar **adicionar entidades** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Adicionar entidades](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Pode verificar se a entidade foi adicionada seguindo os passos na secção, [Obtenha uma única entidade](#get-a-single-entity). Também pode utilizar o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas as entidades para as suas tabelas.

## <a name="get-a-single-entity"></a>Obter uma única entidade

Esta secção ilustra como obter uma única entidade a partir de uma tabela usando a chave de linha da entidade e a chave de partição. 

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos em [Configurar o ambiente](#set-up-the-development-environment)de desenvolvimento, e utiliza dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **GetSingle** que devolve um **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **GetSingle,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço de mesa.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que represente uma referência à tabela a partir da qual está a recuperar a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie um objeto de operação de recuperação que leve um objeto de entidade derivado da **TableEntity**. O primeiro parâmetro é o *partitionKey*, e o segundo parâmetro é o *rowKey*. Utilização da classe **CustomerEntity** e dados apresentados na secção [Adicione um lote de entidades a uma tabela,](#add-a-batch-of-entities-to-a-table)o seguinte código de corte consulta a tabela para uma entidade **ClienteEntity** com um *valor chave* de partilha de "Smith" e um valor chave de *linha* de "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Execute a operação de recuperação.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Passe o resultado para a vista para exibição.

    ```csharp
    return View(result);
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** **as tabelas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **GetSingle** para o nome da visualização e selecione **Adicionar**.

1. Abra `GetSingle.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Executar a aplicação e selecionar **Get Single** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Obter solteiro](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Obter todas as entidades em uma partição

Como mencionado na secção, [Adicione uma entidade a uma tabela,](#add-an-entity-to-a-table)a combinação de uma partição e uma chave de linha identificam exclusivamente uma entidade numa tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que entidades com chaves de partição diferentes. Esta secção ilustra como consultar uma tabela para todas as entidades a partir de uma partição especificada.  

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos em [Configurar o ambiente](#set-up-the-development-environment)de desenvolvimento, e utiliza dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **GetPartition** que devolve um **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **GetPartition,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço de mesa.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que represente uma referência à tabela a partir da qual está a recuperar as entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instantie um objeto **de tablequery** especificando a consulta na cláusula **Onde.** Utilização da classe **CustomerEntity** e dados apresentados na secção [Adicionar um lote de entidades a uma tabela,](#add-a-batch-of-entities-to-a-table)o seguinte código de corte consulta a tabela para todas as entidades onde a Chave **partição** (apelido do cliente) tem um valor de "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Dentro de um loop, ligue para o método **CloudTable.ExecuteQuerySegmented** passando o objeto de consulta que instantaneamente no passo anterior.  O método **CloudTable.ExecuteQuerySegmented** devolve um objeto **TableContinuationToken** que - quando **nulo** - indica que não há mais entidades para recuperar. Dentro do lacete, use outro loop para iterar sobre as entidades devolvidas. No seguinte exemplo de código, cada entidade devolvida é adicionada a uma lista. Uma vez terminada a série, a lista é passada para uma vista para exibição: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** **as tabelas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **A Partição Get** para o nome da visualização e selecione **Adicionar**.

1. Abra `GetPartition.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Executar a aplicação e selecionar **Obter Partition** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Obter Partição](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Eliminar uma entidade

Esta secção ilustra como eliminar uma entidade de uma mesa.

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos em [Configurar o ambiente](#set-up-the-development-environment)de desenvolvimento, e utiliza dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **DeleteEntity** que devolve um **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **DeleteEntity,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço de mesa.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **CloudTable** que represente uma referência à tabela a partir da qual está a apagar a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie um objeto de operação de exclusão que leve um objeto de entidade derivado da **TableEntity**. Neste caso, utilizamos a classe **CustomerEntity** e os dados apresentados na secção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). O **ETag** da entidade deve ser definido com um valor válido.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Executar a operação de eliminação.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Passe o resultado para a vista para exibição.

    ```csharp
    return View(result);
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** **as tabelas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **deleteEntity** para o nome da vista e selecione **Adicionar**.

1. Abra `DeleteEntity.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Executar a aplicação e selecionar **excluir entidade** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Obter solteiro](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Passos seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Inicie-se com o armazenamento de blob Azure e serviços conectados do Estúdio Visual (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Inicie-se com o armazenamento de fila Azure e serviços visual de estúdio conectados (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
