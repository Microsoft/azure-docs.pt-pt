---
title: 'Azure Cosmos DB: Criar uma aplicação de lista de tarefas com Xamarin'
description: Apresenta um exemplo de código Xamarin que pode utilizar para ligar e consultar o Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a940f4bb519332e147577e4a9172406c401d152
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365744"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Quickstart: Construa uma app toda com Xamarin usando a conta API API da Azure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK v4 de Java](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Conector faísca v3](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB.

> [!NOTE]
> O código de exemplo de uma aplicação Xamarin de exemplo canónico completo que mostra várias ofertas do Azure, incluindo CosmosDB, pode ser encontrado no GitHub [aqui](https://github.com/xamarinhq/app-geocontacts). Esta aplicação mostra a visualização de contactos dispersos geograficamente e permite que estes atualizem a respetiva localização.

Este início rápido demonstra como criar uma conta API SQL do Azure Cosmos DB, bases de dados de documentos e contentores com o portal do Azure. Em seguida, você construirá e implementará uma aplicação móvel de lista de toda a lista construída no [SQL .NET API](sql-api-sdk-dotnet.md) e [Xamarin](/xamarin/) utilizando [Xamarin.Forms](/xamarin/) e o [padrão arquitetónico MVVM.](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm)

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="Aplicação de lista de tarefas em execução no iOS":::

## <a name="prerequisites"></a>Pré-requisitos

Se estiver a desenvolver no Windows e ainda não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar a  [Edição Comunitária visual 2019 gratuita do Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa as cargas de trabalho **desenvolvimento do Azure** e **Desenvolvimento móvel com .NET** durante a configuração do Visual Studio.

Se estiver a utilizar um Mac, pode descarregar o Estúdio Visual **gratuito** [para Mac.](https://www.visualstudio.com/vs/mac/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contentor

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar a aplicação Xamarin SQL API do GitHub, rever o código, obter as chaves API e executá-la. Vai ver como é fácil trabalhar com dados programaticamente.

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. No Visual Studio, abra **C:\git-samples\azure-cosmos-db-sql-xamarin-start-start\src\ToDoItems.sln** 

## <a name="obtain-your-api-keys"></a>Obter as chaves da API

Regresse ao portal do Azure para obter as informações de chaves da API e copie-as para a aplicação.

1. No [portal do Azure](https://portal.azure.com/), na sua conta da API de SQL do Azure Cosmos DB, na navegação da esquerda, clique em **Chaves** e em **Chaves de leitura/escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro APIKeys.cs no próximo passo.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, painel Chaves":::

2. Em Visual Studio, abra **ToDoItems.Core/Helpers/APIKeys.cs**.

3. No portal Azure, utilizando o botão de cópia, copie o valor **URI** e faça dele o valor da `CosmosEndpointUrl` variável em APIKeys.cs.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure portal]";
    ```

4. No portal Azure, utilizando o botão de cópia, copie o valor **PRINCIPAL DA CHAVE** e faça dele o valor do .cs `Cosmos Auth Key` APIKeys.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Rever o código

Esta solução demonstra como criar uma aplicação de lista de tarefas com a API de SQL do Azure Cosmos BD e o Xamarin.Forms. A aplicação tem dois separadores. O primeiro contém uma vista de lista que mostra itens de afazeres que ainda não foram concluídos. O segundo apresenta os itens de afazeres que foram concluídos. Para além de poder ver os itens de afazeres que ainda não foram concluídos no primeiro separador, também pode adicionar itens novos, editar já existentes e marcar itens como concluídos.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Copie os dados json e clique em Guardar no Data Explorer no portal do Azure":::

O código na solução ToDoItems contém:

* **ToDoItems.Core**
   * Este é um projeto .NET Standard que detém um projeto Xamarin.Forms e código lógico de aplicação partilhada que mantém todos os itens dentro do Azure Cosmos DB.
* **ToDoItems.Android**
  * Este projeto contém a aplicação Android.
* **ToDoItems.iOS**
  * Este projeto contém a aplicação iOS.

Agora, vamos ver como é que a aplicação comunica com o Azure Cosmos DB.

* É necessário adicionar o pacote NuGet [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) a todos os projetos.
* A `ToDoItem` classe na pasta **ToDoItems.Core/Models** modela os documentos no recipiente **Itens** acima criado. Tenha em conta que a nomenclatura das propriedades é sensível a maiúsculas e minúsculas.
* A `CosmosDBService` classe na pasta **ToDoItems.Core/Services** encapsula a comunicação à Azure Cosmos DB.
* Na classe `CosmosDBService` há uma variável de tipo `DocumentClient`. O `DocumentClient` é usado para configurar e executar pedidos contra a conta DB da Azure Cosmos, e é instantâneo:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Ao consultar um recipiente para obter documentos, o `DocumentClient.CreateDocumentQuery<T>` método é utilizado, como visto aqui na `CosmosDBService.GetToDoItems` função:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    O `CreateDocumentQuery<T>` URI leva um URI que aponta para o recipiente criado na secção anterior. E também pode especificar operadores LINQ, como, por exemplo, uma cláusula `Where`. Neste caso, só são devolvidos os itens de afazeres que não estão concluídos.

    A função `CreateDocumentQuery<T>` é executada de forma síncrona e devolve `IQueryable<T>`. No entanto, o `AsDocumentQuery` método converte o de um `IQueryable<T>` `IDocumentQuery<T>` objeto, que pode ser executado assíncroticamente. Deste modo, o thread da IU das aplicações móveis não é bloqueado.

    A `IDocumentQuery<T>.ExecuteNextAsync<T>` função recupera a página dos resultados da Azure Cosmos DB, que `HasMoreResults` examinará para ver se ainda há resultados adicionais a serem devolvidos.

> [!TIP]
> Várias funções que operam em contentores e documentos da Azure Cosmos tomam um URI como parâmetro que especifica o endereço do recipiente ou documento. Este URI é construído de acordo com a classe `URIFactory`. UrIs para bases de dados, contentores e documentos podem ser criados com esta classe.

* A `ComsmosDBService.InsertToDoItem` função demonstra como inserir um novo documento:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    O item URI é especificado, bem como o item a ser inserido.

* A `CosmosDBService.UpdateToDoItem` função demonstra como substituir um documento existente por um novo:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Aqui é necessário um novo URI para identificar exclusivamente o documento para substituir e é obtido utilizando `UriFactory.CreateDocumentUri` e passando-lhe a base de dados e os nomes dos contentores e o ID do documento.

    `DocumentClient.ReplaceDocumentAsync` substitui o documento identificado pelo URI pelo que foi especificado como parâmetro.

* A eliminação de um item é demonstrada com a `CosmosDBService.DeleteToDoItem` função:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Note mais uma vez o documento único que uri está sendo criado e passado para a `DocumentClient.DeleteDocumentAsync` função.

## <a name="run-the-app"></a>Executar a aplicação

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB.

Os seguintes passos demonstram como executar a aplicação com o depurador do Visual Studio para Mac.

> [!NOTE]
> A utilização da versão da aplicação para Android é exatamente igual. Eventuais diferenças serão destacadas nos passos abaixo. Se quiser depurar com o Visual Studio no Windows, está disponível documentação para tal para [iOS aqui](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) e [Android aqui](/xamarin/android/deploy-test/debugging/).

1. Primeiro, selecione a plataforma que quer segmentar ao clicar no menu pendente realçado e selecionar ToDoItems.iOS, para iOS, ou ToDoItems.Android, para Android.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Selecionar uma plataforma para depurar no Visual Studio para Mac":::

2. Para começar a depurar a aplicação, prima cmd+Enter ou clique no botão de reprodução.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Começar a depurar no Visual Studio para Mac":::

3. Quando o simulador iOS ou o emulador Android terminarem o lançamento, a aplicação apresentará dois separadores na parte inferior do ecrã para iOS e o topo do ecrã para Android. O primeiro mostra todos os itens, que não estão concluídos, o segundo mostra todos os itens, que estão concluídos.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="Iniciar o ecrã da aplicação de tarefas":::

4. Para concluir um item de afazer no iOS, deslize-o para a esquerda > toque no botão **Concluir**. Para concluir um item de afazer no Android, prima-o > toque no botão de conclusão.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Concluir um item de afazer":::

5. Para editar um item de afazer > toque no item > é apresentado um ecrã novo, que lhe permite introduzir valores novos. Tocar no botão Guardar aplicará as alterações no Azure Cosmos DB.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Editar item de afazer":::

6. Para adicionar um item de afazer > toque no botão **Adicionar**, no canto superior direito do ecrã principal > é apresentada uma página de edição em branco nova.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Adicionar item de afazer":::

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta Azure Cosmos, criar um recipiente usando o Data Explorer e construir e implementar uma aplicação Xamarin. Pode agora importar mais dados para a sua conta Azure Cosmos.

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
