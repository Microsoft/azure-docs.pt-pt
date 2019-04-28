---
title: Criar uma aplicação web .NET com o Azure Cosmos DB com a API SQL
description: Neste início rápido, vai utilizar o portal do Azure e uma aplicação web .NET para criar e gerir recursos de conta SQL API no Azure Cosmos DB.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 7ecb2269243ae96b629a20a26956e6220a2e616c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120270"
---
# <a name="quickstart-build-a-net-web-app-using-sql-api-account-in-azure-cosmos-db"></a>Início rápido: Criar uma aplicação de web do .NET com a conta de API de SQL no Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (pré-visualização)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode utilizar o Azure Cosmos DB para criar rapidamente e consultar bases de dados de chave/valor, bases de dados do documento e bases de dados de gráfico, que beneficiam de capacidades de escalamento horizontal no núcleo do Azure Cosmos DB e distribuição global. 

Este início rápido demonstra como utilizar o portal do Azure para criar um Azure Cosmos DB [API do SQL](sql-api-introduction.md) de contas, criar uma base de dados de documentos e uma coleção e adicionar dados à coleção. Em seguida, utilizar um [SDK de .NET de SQL](sql-api-sdk-dotnet.md) aplicação web para adicionar mais dados à coleção. 

Neste início rápido, utilize o Data Explorer no portal do Azure para criar a base de dados e a coleção. Também pode criar a base de dados e a coleção com o código de exemplo do .NET. Para obter mais informações, consulte [reveja o código .NET](#review-the-net-code). 

## <a name="prerequisites"></a>Pré-requisitos

Visual Studio 2017 com o fluxo de trabalho de desenvolvimento do Azure instalado
- Pode transferir e utilizar o **gratuita** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio. 

Uma subscrição do Azure ou a conta de avaliação gratuita do Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>Adicionar uma base de dados e uma coleção 

Pode utilizar o Data Explorer no portal do Azure para criar uma base de dados e uma coleção. 

1.  Selecione **Data Explorer** no painel de navegação esquerda no seu Azure Cosmos DB a página de conta e, em seguida, selecione **nova coleção**. 
    
    Poderá ter de se deslocar para a direita para vê a **adicionar coleção** área.
    
    ![O portal do Azure Data Explorer, no painel Adicionar coleção](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  Na página **Adicionar coleção**, introduza as definições para a nova coleção.
    
    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**Id de base de dados**|ToDoList|Introduza *ToDoList* como o nome para a nova base de dados. Nomes de base de dados tem de conter entre 1 e 255 carateres e não podem conter `/, \\, #, ?`, ou um espaço à direita.|
    |**Id da coleção**|Itens|Denomine a nova coleção como *Itens*. Os IDs das coleções têm os mesmos requisitos como nomes de base de dados.|
    |**Chave de partição**| /categoria| O exemplo descrito neste artigo usa */category* como a chave de partição.|
    |**Débito**|400|Deixe o débito com 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    
    Não adicione **chaves exclusivas** para este exemplo. Chaves exclusivas permitem-lhe adicionar uma camada de integridade dos dados na base de dados ao garantir a exclusividade de um ou mais valores por chave de partição. Para obter mais informações, consulte [chaves exclusivas no Azure Cosmos DB](unique-keys.md).
    
1.  Selecione **OK**. 
    O Data Explorer mostra a base de dados e a coleção novas.
    
    ![O Data Explorer do portal do Azure a mostrar a base de dados e a coleção novas](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

## <a name="add-data-to-your-database"></a>Adicionar dados à sua base de dados

Adicione dados à sua nova base de dados com o Data Explorer.

1. Na **Data Explorer**, a base de dados nova aparece no **coleções** painel. Expanda a **ToDoList** bases de dados, expanda o **itens** coleção, selecione **documentos**e, em seguida, selecione **novo documento**. 
   
   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Adicione a seguinte estrutura para o documento no lado direito do **documentos** painel:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Selecione **Guardar**.
   
   ![Copiar dados json e selecione guardar no Data Explorer no portal do Azure](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Selecione **novo documento** novamente e criar e guardar outro documento com um único `id`e quaisquer outras propriedades e valores quer. Os documentos podem ter qualquer estrutura, porque o Azure Cosmos DB não impõe qualquer esquema aos seus dados.

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="use-the-net-web-app-to-manage-data"></a>Utilizar a aplicação web do .NET para gerir dados

Para ver como é fácil trabalhar programaticamente com os seus dados do Azure Cosmos DB, clone a aplicação de web de .NET API de SQL de exemplo do GitHub, atualizar a cadeia de ligação e executar a aplicação para atualizar os seus dados. 

Também é possível criar a base de dados e a coleção com o código de exemplo do .NET. Para obter mais informações, consulte [reveja o código .NET](#review-the-net-code).

### <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Em primeiro lugar, clone um C# [aplicação da SQL API](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) do GitHub. 

1. Abra uma janela de terminal do git, tal como Git Bash, criar um novo diretório com o nome *git-samples*e altere a ele: 
   
   ```bash
   mkdir /c/git-samples/
   cd /c/git-samples/
   ```
   
1. Execute o seguinte comando para clonar o repositório de exemplo e criar uma cópia da aplicação de exemplo no seu computador:
   
   ```bash
   git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
   ```

### <a name="update-the-connection-string"></a>Atualizar a cadeia de ligação 

1. Navegue para e abra o *todo.sln* ficheiros da sua aplicação clonado no Visual Studio. 

1. No Visual Studio **Explorador de soluções**, abra o *Web. config* ficheiro. 

1. Volte ao portal do Azure para copiar as informações da cadeia de ligação para colar os *Web. config*.
   
   1. No seu Azure Cosmos DB conta de navegação esquerdo, selecione **chaves**.
      
      ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)
      
   1. Sob **chaves de leitura / escrita**, cópia a **URI** com o botão Copiar no lado direito de valor e cole-o no `endpoint` chave o *Web. config*. Por exemplo: 
      
      `<add key="endpoint" value="https://mysqlapicosmosdb.documents.azure.com:443/" />`
      
   1. Cópia a **chave primária** valor e cole-o no `authKey` chave a *Web. config*. Por exemplo:
      
      `<add key="authKey" value="19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==" />`

       
1. Certificar-se de que os valores da base de dados e coleção da *Web. config* correspondem aos nomes que criou anteriormente. 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
1. Guardar o *Web. config.* Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB.

### <a name="run-the-web-app"></a>Executar a aplicação Web

1. No Visual Studio, clique com botão direito a **todo** projeto **Explorador de soluções**e, em seguida, selecione **gerir pacotes NuGet**. 

1. Na caixa **Procurar** do NuGet, escreva *DocumentDB*.

1. Nos resultados, instale o **Microsoft.Azure.DocumentDB** biblioteca se ainda não estiver instalado. Esta ação instala o [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) pacote e todas as dependências.
   
   Se o Gestor de pacotes de NuGet exibe uma mensagem que faltam alguns pacotes da solução, selecione **restaurar** instalá-los a partir de origens internas. 

1. Selecione **Ctrl**+**F5** para executar a aplicação no seu browser. 

1. Selecione **criar novo** na aplicação de tarefas pendentes e crie algumas tarefas novas.

   ![Aplicação de Lista A Fazer com dados de exemplo](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

Pode voltar ao Data Explorer no portal do Azure para ver, consultar, modificar e trabalhar com os seus dados de novo. 

## <a name="review-the-net-code"></a>Rever o código do .NET

Este passo é opcional. Neste início rápido, criou uma base de dados e uma coleção no portal do Azure e adicionar dados de exemplo utilizando o exemplo de .NET. No entanto, também pode criar a base de dados e a coleção utilizando o exemplo de .NET. Reveja os seguintes fragmentos se estiver interessado em como os recursos de base de dados são criados no código. Os fragmentos são obtidos a partir do *DocumentDBRepository.cs* do ficheiro no **todo** projeto.

* Esse código inicializa o `DocumentClient`: 

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* Este código cria a nova base de dados utilizando o `CreateDatabaseAsync` método:

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* O código a seguir cria a nova coleção com o `CreateDocumentCollectionAsync` método:

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync()
    {
        try
        {
           await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
        }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
           {
              await client.CreateDocumentCollectionAsync(
              UriFactory.CreateDatabaseUri(DatabaseId),
              new DocumentCollection
              {
                  Id = CollectionId
              },
              new RequestOptions { OfferThroughput = 400 });
           }
           else
           {
             throw;
           }
        }
    }
    ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar uma conta do Azure Cosmos DB, criar uma base de dados e uma coleção com o Data Explorer e executar uma aplicação web do .NET para atualizar os seus dados. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).

