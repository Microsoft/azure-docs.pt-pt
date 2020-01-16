---
title: Introdução ao armazenamento de tabelas do Azure usando o Visual Studio (ASP.NET)
description: Como começar a usar o armazenamento de tabelas do Azure em um projeto ASP.NET no Visual Studio depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979633"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao armazenamento de tabelas do Azure e aos serviços conectados do Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

O armazenamento de tabelas do Azure permite que você armazene grandes quantidades de dados estruturados. O serviço é um repositório de armazenamento NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Este tutorial mostra como escrever código ASP.NET para alguns cenários comuns usando entidades de armazenamento de tabelas do Azure. Esses cenários incluem a criação de uma tabela e a adição, a consulta e a exclusão de entidades de tabela. 

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Gerenciador de soluções**, clique com o botão direito do mouse em **controladores**e, no menu de contexto, selecione **Adicionar-> controlador**.

    ![Adicionar um controlador a um aplicativo MVC ASP.NET](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Na caixa de diálogo **Adicionar Scaffold** , selecione **controlador MVC 5 – vazio**e selecione **Adicionar**.

    ![Especificar tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Na caixa de diálogo **Adicionar controlador** , nomeie o controlador *TablesController*e selecione **Adicionar**.

    ![Nomear o controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adicione as seguintes diretivas *using* ao arquivo de `TablesController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Criar uma classe de modelo

Muitos dos exemplos neste artigo usam uma classe derivada de **TableEntity**chamada **CustomerEntity**. As etapas a seguir o orientarão na declaração desta classe como uma classe de modelo:

1. No **Gerenciador de soluções**, clique com o botão direito do mouse em **modelos**e, no menu de contexto, selecione **classe add->** .

1. Na caixa de diálogo **Adicionar novo item** , nomeie a classe **CustomerEntity**.

1. Abra o arquivo `CustomerEntity.cs` e adicione o seguinte **usando** a diretiva:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Modifique a classe para que, quando terminar, a classe seja declarada como no código a seguir. A classe declara uma classe de entidade chamada **CustomerEntity** que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

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

As etapas a seguir ilustram como criar uma tabela:

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **CreateTable** que retorna um **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **CreateTable** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (alterar *&lt;nome da conta* de armazenamento > para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **cloudtable** que representa uma referência ao nome da tabela desejada. O método **CloudTableClient. GetTableReference** não faz uma solicitação no armazenamento de tabelas. A referência é retornada se a tabela existir ou não. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Chame o método **cloudtable. CreateIfNotExists** para criar a tabela, caso ela ainda não exista. O método **cloudtable. CreateIfNotExists** retornará **true** se a tabela não existir e for criada com êxito. Caso contrário, **false** será retornado.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Atualize o **ViewBag** com o nome da tabela.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições** , clique com o botão direito do mouse em **tabelas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , insira **criartable** para o nome da exibição e selecione **Adicionar**.

1. Abra `CreateTable.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **criar tabela** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Criar tabela](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Como mencionado anteriormente, o método **cloudtable. CreateIfNotExists** retorna **true** somente quando a tabela não existe e é criada. Portanto, se você executar o aplicativo quando a tabela existir, o método retornará **false**. Para executar o aplicativo várias vezes, você deve excluir a tabela antes de executar o aplicativo novamente. A exclusão da tabela pode ser feita por meio do método **cloudtable. Delete** . Você também pode excluir a tabela usando o [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

As *entidades* são mapeadas para objetos C\# usando uma classe personalizada derivada de **TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. Nesta seção, você verá como definir uma classe de entidade que usa o primeiro nome do cliente como a chave de linha e o sobrenome como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes, mas a utilização de várias chaves de partição permite uma maior escalabilidade de operações simultâneas. Para qualquer propriedade que deve ser armazenada no serviço tabela, a propriedade deve ser uma propriedade pública de um tipo com suporte que expõe a configuração e a recuperação de valores.
A classe de entidade *deve* declarar um construtor público sem parâmetros.

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o ficheiro `TablesController.cs`.

1. Adicione a seguinte diretiva para que o código no arquivo de `TablesController.cs` possa acessar a classe **CustomerEntity** :

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adicione um método chamado **AddEntity** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **AddEntity** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (alterar *&lt;nome da conta* de armazenamento > para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **cloudtable** que representa uma referência à tabela à qual você vai adicionar a nova entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie uma instância e inicialize a classe **CustomerEntity** .

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Crie um objeto **TableOperation** que insere a entidade Customer.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Execute a operação de inserção chamando o método **cloudtable. Execute** . Você pode verificar o resultado da operação inspecionando a propriedade **TableResult. HttpStatusCode** . Um código de status de 2xx indica que a ação solicitada pelo cliente foi processada com êxito. Por exemplo, inserções bem-sucedidas de novas entidades resultam em um código de status HTTP de 204, o que significa que a operação foi processada com êxito e o servidor não retornou nenhum conteúdo.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Atualize o **ViewBag** com o nome da tabela e os resultados da operação de inserção.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições** , clique com o botão direito do mouse em **tabelas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , insira **AddEntity** para o nome da exibição e selecione **Adicionar**.

1. Abra `AddEntity.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **Adicionar entidade** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Adicionar entidade](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Você pode verificar se a entidade foi adicionada seguindo as etapas na seção [obter uma única entidade](#get-a-single-entity). Você também pode usar o [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para exibir todas as entidades de suas tabelas.

## <a name="add-a-batch-of-entities-to-a-table"></a>Adicionar um lote de entidades a uma tabela

Além de poder [Adicionar uma entidade a uma tabela por vez](#add-an-entity-to-a-table), você também pode adicionar entidades no lote. A adição de entidades no lote reduz o número de viagens de ida e volta entre o código e o serviço tabela do Azure. As etapas a seguir ilustram como adicionar várias entidades a uma tabela com uma única operação de inserção:

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment).

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **Addentitiess** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **addentities** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (alterar *&lt;nome da conta* de armazenamento > para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **cloudtable** que representa uma referência à tabela à qual você vai adicionar as novas entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie uma instância de alguns objetos Customer com base na classe de modelo **CustomerEntity** apresentada na seção, [adicione uma entidade a uma tabela](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Obtenha um objeto **TableBatchOperation** .

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Adicione entidades ao objeto de operação de inserção em lote.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Execute a operação de inserção em lote chamando o método **cloudtable. ExecuteBatch** .   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. O método **cloudtable. ExecuteBatch** retorna uma lista de objetos **TableResult** em que cada objeto **TableResult** pode ser examinado para determinar o êxito ou a falha de cada operação individual. Para este exemplo, passe a lista para uma exibição e permita que a exibição exiba os resultados de cada operação. 
 
    ```csharp
    return View(results);
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições** , clique com o botão direito do mouse em **tabelas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , insira **addentities** para o nome da exibição e selecione **Adicionar**.

1. Abra `AddEntities.cshtml`e modifique-o para que fique semelhante ao seguinte.

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **adicionar entidades** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Adicionar entidades](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Você pode verificar se a entidade foi adicionada seguindo as etapas na seção [obter uma única entidade](#get-a-single-entity). Você também pode usar o [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para exibir todas as entidades de suas tabelas.

## <a name="get-a-single-entity"></a>Obter uma única entidade

Esta seção ilustra como obter uma única entidade de uma tabela usando a chave de linha e a chave de partição da entidade. 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e usa dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **GetSingle** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **GetSingle** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (alterar *&lt;nome da conta* de armazenamento > para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **cloudtable** que representa uma referência à tabela da qual você está recuperando a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie um objeto de operação de recuperação que usa um objeto de entidade derivado de **TableEntity**. O primeiro parâmetro é o *partitionKey*e o segundo parâmetro é o *rowKey*. Usando a classe **CustomerEntity** e os dados apresentados na seção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o trecho de código a seguir consulta a tabela em busca de uma entidade **CustomerEntity** com um valor *partitionKey* de "Smith" e um valor *rowKey* de "Ben":

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições** , clique com o botão direito do mouse em **tabelas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , insira **GetSingle** para o nome da exibição e selecione **Adicionar**.

1. Abra `GetSingle.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **obter único** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Obter único](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Obter todas as entidades em uma partição

Conforme mencionado na seção, [adicione uma entidade a uma tabela](#add-an-entity-to-a-table), a combinação de uma partição e uma chave de linha identificam exclusivamente uma entidade em uma tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que as entidades com chaves de partição diferentes. Esta seção ilustra como consultar uma tabela para todas as entidades de uma partição especificada.  

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e usa dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **Getpartition** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **Getpartition** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (alterar *&lt;nome da conta* de armazenamento > para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **cloudtable** que representa uma referência à tabela da qual você está recuperando as entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie uma instância de um objeto **TableQuery** especificando a consulta na cláusula **Where** . Usando a classe **CustomerEntity** e os dados apresentados na seção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o trecho de código a seguir consulta a tabela em busca de todas as entidades em que o **PartitionKey** (sobrenome do cliente) tem um valor de "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Dentro de um loop, chame o método **cloudtable. ExecuteQuerySegmented** passando o objeto de consulta que você instanciou na etapa anterior.  O método **cloudtable. ExecuteQuerySegmented** retorna um objeto **TableContinuationToken** que-quando **NULL** -indica que não há mais entidades a serem recuperadas. Dentro do loop, use outro loop para iterar nas entidades retornadas. No exemplo de código a seguir, cada entidade retornada é adicionada a uma lista. Depois que o loop termina, a lista é passada para uma exibição para exibição: 

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições** , clique com o botão direito do mouse em **tabelas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , insira **getpartition** para o nome da exibição e selecione **Adicionar**.

1. Abra `GetPartition.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **obter partição** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Obter partição](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Eliminar uma entidade

Esta seção ilustra como excluir uma entidade de uma tabela.

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas em [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment)e usa dados de [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Abra o ficheiro `TablesController.cs`.

1. Adicione um método chamado **DeleteEntity** que retorna um **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **DeleteEntity** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (alterar *&lt;nome da conta* de armazenamento > para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudTableClient** representa um cliente de serviço tabela.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto **cloudtable** que representa uma referência à tabela da qual você está excluindo a entidade. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Crie um objeto de operação de exclusão que usa um objeto de entidade derivado de **TableEntity**. Nesse caso, usamos a classe **CustomerEntity** e os dados apresentados na seção [adicionam um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). A **ETag** da entidade deve ser definida como um valor válido.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Execute a operação de exclusão.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Passe o resultado para a exibição para exibição.

    ```csharp
    return View(result);
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições** , clique com o botão direito do mouse em **tabelas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , digite **DeleteEntity** para o nome da exibição e selecione **Adicionar**.

1. Abra `DeleteEntity.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Execute o aplicativo e selecione **excluir entidade** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Obter único](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Passos seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao armazenamento de BLOBs do Azure e aos serviços conectados do Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao armazenamento de filas do Azure e aos serviços conectados do Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
