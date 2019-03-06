---
title: Introdução ao armazenamento de tabelas do Azure e o Visual Studio ligado serviços (ASP.NET) | Documentos da Microsoft
description: Como começar a utilizar o armazenamento de tabelas do Azure num projeto do ASP.NET no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio ligado Services
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 1a35d1cbd49a55b0c25f5e2b993358130589c125
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444638"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao armazenamento de tabelas do Azure e o Visual Studio ligado serviços (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de tabelas do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da cloud do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Este tutorial mostra como escrever código do ASP.NET para alguns cenários comuns com entidades de armazenamento de tabelas do Azure. Estes cenários incluem a criação de tabelas e adicionar, consultar e eliminar entidades da tabela. 

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. Na **Explorador de soluções**, clique com botão direito **controladores**e, no menu de contexto, selecione **adicionar -> controlador**.

    ![Adicionar um controlador a uma aplicação ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Sobre o **Adicionar estrutura** caixa de diálogo, selecione **controlador MVC 5 – vazio**e selecione **Add**.

    ![Especifique o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Sobre o **Add Controller** caixa de diálogo, o nome do controlador *TablesController*e selecione **adicionar**.

    ![Nome do controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adicione as seguintes *usando* diretivas para o `TablesController.cs` ficheiro:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Criar uma classe de modelo

Muitos dos exemplos neste artigo utilizam uma **TableEntity**-derivado da classe chamada **CustomerEntity**. Os seguintes passos guiá-lo por meio de declarar essa classe como uma classe de modelo:

1. Na **Explorador de soluções**, clique com botão direito **modelos**e, no menu de contexto, selecione **adicionar -> classe**.

1. Sobre o **Adicionar Novo Item** caixa de diálogo, o nome da classe **CustomerEntity**.

1. Abra o `CustomerEntity.cs` de ficheiros e adicione as seguintes **usando** diretiva:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Modificar a classe, de modo a que, quando terminar, a classe é declarada como no seguinte código. A classe declara uma classe de entidade chamada **CustomerEntity** que utiliza o nome próprio do cliente como a chave de linha e o apelido como a chave de partição.

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

Os passos seguintes mostram como criar uma tabela:

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicionar um método chamado **CreateTable** que retorna um **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro de **CreateTable** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para o nome da tabela pretendida. O **CloudTableClient.GetTableReference** método não faz um pedido de armazenamento de tabelas. A referência é devolvida se a tabela existe ou não. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Chamar o **CloudTable.CreateIfNotExists** método para criar a tabela se ainda não existir. O **CloudTable.CreateIfNotExists** retorno do método **verdadeiro** se a tabela não existe e é criada com êxito. Caso contrário, **false** é devolvido.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Atualização do **ViewBag** com o nome da tabela.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **CreateTable** para o nome da vista, selecione **Add**.

1. Abra `CreateTable.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Executar a aplicação e selecione **criar tabela** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Criar tabela](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Como mencionado anteriormente, o **CloudTable.CreateIfNotExists** retorno do método **verdadeiro** apenas quando a tabela não existe e é criada. Por conseguinte, se executar a aplicação quando a tabela existe, o método retorna **false**. Para executar a aplicação com várias vezes, tem de eliminar a tabela antes de executar a aplicação novamente. A eliminar a tabela pode ser feita através da **CloudTable.Delete** método. Também pode eliminar a tabela com o [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

*Entidades* mapa c\# objetos através de uma classe personalizada derivam **TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. Nesta seção, verá como definir uma classe de entidade que utiliza o nome próprio do cliente como a chave de linha e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes, mas a utilização de várias chaves de partição permite uma maior escalabilidade de operações simultâneas. Para qualquer propriedade que deva ser armazenada no serviço tabela, a propriedade tem de ser uma propriedade pública de um tipo suportado que expõe valores de obtenção e de definição.
A classe de entidade *tem* declarar um construtor sem parâmetros público.

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o ficheiro `TablesController.cs`.

1. Adicione a seguinte diretiva para que o código na `TablesController.cs` podem aceder a ficheiros a **CustomerEntity** classe:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adicionar um método chamado **AddEntity** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro de **AddEntity** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela à qual pretende adicionar a nova entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanciar e inicializar o **CustomerEntity** classe.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Criar uma **TableOperation** objeto que insere a entidade customer.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Executar a operação de inserção, chamando o **Cloudtable** método. Pode verificar o resultado da operação ao inspecionar o **TableResult.HttpStatusCode** propriedade. Um código de estado de 2xx indica a ação solicitada pelo cliente foi processada com êxito. Por exemplo, com êxito inserções de novas entidades resulta num código de estado HTTP de 204, que significa que a operação foi processada com êxito e o servidor não devolveu qualquer conteúdo.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Atualização do **ViewBag** com o nome da tabela e os resultados da operação de inserção.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **AddEntity** para o nome da vista, selecione **Add**.

1. Abra `AddEntity.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Executar a aplicação e selecione **adicionar entidade** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Adicionar entidade](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Pode verificar se a entidade foi adicionada ao seguir os passos na secção, [obter uma única entidade](#get-a-single-entity). Também pode utilizar o [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas as entidades para as suas tabelas.

## <a name="add-a-batch-of-entities-to-a-table"></a>Adicionar um lote de entidades a uma tabela

Além de poder [adicionar uma entidade a uma tabela de uma por vez](#add-an-entity-to-a-table), também pode adicionar entidades no batch. Adicionar entidades no batch reduz o número de viagens de ida e volta entre o código e o serviço de tabela do Azure. Os passos seguintes mostram como adicionar a operação de inserção de várias entidades a uma tabela com um único:

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o ficheiro `TablesController.cs`.

1. Adicionar um método chamado **AddEntities** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro de **AddEntities** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela à qual pretende adicionar as novas entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Criar uma instância de alguns objetos de cliente com base na **CustomerEntity** apresentado na secção, de classe de modelo [adicionar uma entidade a uma tabela](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Obter um **TableBatchOperation** objeto.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Adicione entidades para o objeto de operação de inserção de lote.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Executar a operação de inserção de batch ao chamar o **CloudTable.ExecuteBatch** método.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. O **CloudTable.ExecuteBatch** método retorna uma lista de **TableResult** objetos em que cada **TableResult** objeto pode ser examinado para determinar o sucesso ou fracasso de cada operação individual. Neste exemplo, passar a lista para uma exibição e deixar que o modo de exibição exibir os resultados de cada operação. 
 
    ```csharp
    return View(results);
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **AddEntities** para o nome da vista, selecione **Add**.

1. Abra `AddEntities.cshtml`e modificá-lo para que ele é semelhante ao seguinte.

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

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Executar a aplicação e selecione **adicionar entidades** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Adicionar entidades](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Pode verificar se a entidade foi adicionada ao seguir os passos na secção, [obter uma única entidade](#get-a-single-entity). Também pode utilizar o [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas as entidades para as suas tabelas.

## <a name="get-a-single-entity"></a>Obter uma única entidade

Esta secção ilustra como obter uma única entidade de uma tabela com a chave de linha da entidade e a chave de partição. 

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e utiliza dados da [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicionar um método chamado **GetSingle** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro de **GetSingle** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela a partir da qual estão a obter a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Criar um objeto de operação de recuperação que usa um objeto de entidade derivado **TableEntity**. O primeiro parâmetro é o *partitionKey*, e o segundo parâmetro é o *rowKey*. Utilizar o **CustomerEntity** classe e os dados apresentados na secção [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o fragmento de código seguinte consulta a tabela para um **CustomerEntity**entidade com um *partitionKey* valor de "Smith" e um *rowKey* valor de "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Execute a operação de recuperação.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Passe o resultado para a exibição para exibição.

    ```csharp
    return View(result);
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **GetSingle** para o nome da vista, selecione **Add**.

1. Abra `GetSingle.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

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

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Executar a aplicação e selecione **obter único** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Obter único](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Obter todas as entidades numa partição

Conforme mencionado na seção, [adicionar uma entidade a uma tabela](#add-an-entity-to-a-table), a combinação de uma partição e uma chave de linha identificar exclusivamente uma entidade numa tabela. Entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes. Esta secção ilustra como consultar uma tabela para todas as entidades de uma partição especificada.  

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e utiliza dados da [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicionar um método chamado **GetPartition** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro de **GetPartition** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela a partir da qual estão a obter as entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Criar uma instância de um **TableQuery** objeto especificando a consulta no **onde** cláusula. Utilizar o **CustomerEntity** classe e os dados apresentados na secção [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o fragmento de código seguinte consulta a tabela para uma todas as entidades em que o  **PartitionKey** (apelido do cliente) tem um valor de "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Dentro de um loop, chamar o **CloudTable.ExecuteQuerySegmented** método passando o objeto de consulta instanciado no passo anterior.  O **CloudTable.ExecuteQuerySegmented** método devolve um **TableContinuationToken** objeto que - quando **nulo** -indica que não há nenhuma mais entidades para recuperar. Dentro do loop, utilize outro loop para iterar sobre as entidades retornadas. No exemplo de código a seguir, cada entidade retornada é adicionada a uma lista. Depois de terminar o loop, a lista é passado para uma vista para exibição: 

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

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **GetPartition** para o nome da vista, selecione **Add**.

1. Abra `GetPartition.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

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

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Executar a aplicação e selecione **partição obter** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Obter a partição](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Eliminar uma entidade

Esta secção ilustra como eliminar uma entidade a partir de uma tabela.

> [!NOTE]
> 
> Esta secção assume que concluiu os passos em [configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e utiliza dados da [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicionar um método chamado **DeleteEntity** que retorna um **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro de **DeleteEntity** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudTableClient** objeto representa um cliente do serviço de tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um **CloudTable** objeto que representa uma referência para a tabela a partir do qual está a eliminar a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Criar um objeto de operação de eliminação que usa um objeto de entidade derivado **TableEntity**. Neste caso, vamos utilizar o **CustomerEntity** classe e os dados apresentados na secção [adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). A entidade **ETag** tem de ser definido para um valor válido.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Execute a operação de eliminação.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Passe o resultado para a exibição para exibição.

    ```csharp
    return View(result);
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **tabelas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **DeleteEntity** para o nome da vista, selecione **Add**.

1. Abra `DeleteEntity.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

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

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Executar a aplicação e selecione **eliminar entidade** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Obter único](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Passos Seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao armazenamento de Blobs do Azure e o Visual Studio ligado serviços (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
