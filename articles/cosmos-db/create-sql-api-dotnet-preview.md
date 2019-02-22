---
title: 'Início rápido: Criar uma aplicação web ASP.NET com o Azure Cosmos DB com a API de SQL e o portal do Azure'
description: Neste início rápido, utilize a API de SQL do Azure Cosmos DB e o portal do Azure para criar uma aplicação web ASP.NET
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 11b89cd33346c2d6da2269bbc2a4b9550f3120b3
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587390"
---
# <a name="quickstart-build-an-aspnet-web-app-using-azure-cosmos-db-sql-api-account"></a>Início rápido: Criar uma aplicação de web ASP.NET com a conta do Azure Cosmos DB SQL API

> [!div class="op_single_selector"]
> * [.NET (pré-visualização)](create-sql-api-dotnet-preview.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

O Azure Cosmos DB é o serviço de base de dados de elevada disponibilidade de vários modelos distribuída globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este início rápido demonstra como criar um Azure Cosmos DB [API do SQL](sql-api-introduction.md) conta, base de dados e de contentor no portal do Azure. Em seguida, vai criar e implementar uma aplicação web de lista de tarefas do ASP.NET criada com o [SQL .NET API](sql-api-sdk-dotnet.md), conforme mostrado na captura de ecrã seguinte. 

Este guia de introdução utiliza a versão 3.0 + do SDK de .NET do Azure Cosmos DB. 

![Aplicação de Lista A Fazer com dados de exemplo](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list-preview.png)

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Certifique-se de que ativa o **desenvolvimento do Azure** carga de trabalho durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

<a id="create-collection"></a>
## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection-preview](../../includes/cosmos-db-create-collection-preview.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma [aplicação da SQL API a partir do GitHub](https://github.com/Azure-Samples/cosmos-dotnet-todo-app), definir a cadeia de ligação e executá-la. 

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/cosmos-dotnet-todo-app.git
    ```

4. Abra o **todo.sln** arquivo da solução no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Tenha em atenção de que se estiver familiarizado com a versão anterior do SDK do .NET, poderá ser utilizado para ver os termos "coleção' e 'document'. Como o Azure Cosmos DB suporta vários modelos de API, versão 3.0 + do SDK do .NET usa os termos genéricos "contentor" e "item". Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. [Saiba mais sobre as bases de dados, contentores e itens.](databases-containers-items.md)

Os seguintes fragmentos são retirados do ficheiro ToDoItemService.cs.

* O CosmosClient é inicializado em linhas 68 69.

    ```csharp
    CosmosConfiguration config = new CosmosConfiguration(Endpoint, PrimaryKey);
    client = new CosmosClient(config);
    ```

* Uma nova base de dados é criada na linha 71.

    ```csharp
    CosmosDatabase database = await client.Databases.CreateDatabaseIfNotExistsAsync(DatabaseId);
    ```

* Um novo contentor é criado na linha 72 com chave de partição "/ categoria."

    ```csharp
    CosmosContainer container = await database.Containers.CreateContainerIfNotExistsAsync(ContainerId, "/category");
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](https://portal.azure.com/), na sua conta do Azure Cosmos DB, na navegação da esquerda, selecione **Chaves** e, em seguida, selecione **Chaves de leitura/escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro web.config no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)

2. No Visual Studio 2017, abra a **Web. config** ficheiro. 

3. Copie o valor do URI a partir do portal (com o botão Copiar) e faça do valor da ``endpoint`` chave em Web. config. 

    `<add key="endpoint" value="FILLME" />`

4. Em seguida, copie o valor de chave primária do portal e faça do valor da ``primarykey`` em Web. config. 

    `<add key="primaryKey" value="FILLME" />`
    
5. Em seguida, atualize o valor de base de dados e um contentor de acordo com o nome da base de dados e do contentor que criou anteriormente. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `<add key="database" value="Tasks" />`

    `<add key="container" value="Items" />`
    
## <a name="run-the-web-app"></a>Executar a aplicação Web

1. Na **Explorador de soluções**, clique com o botão direito do rato no seu novo projeto de aplicativo de console, o que está sob a sua solução Visual Studio, e, em seguida, clique em **gerir pacotes NuGet...**
    
    ![Captura de ecrã do Menu com Botão do Lado Direito para o Projeto](./media/create-sql-api-dotnet/manage-nuget-package.png)
1. Na **NuGet** separador, clique em **procurar**e o tipo **Microsoft.Azure.Cosmos** na caixa de pesquisa.
1. Nos resultados, localize **Microsoft.Azure.Cosmos** e clique em **instalar**.
   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

   ![Captura de ecrã do NuGet Menu para encontrar o SDK de cliente do Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Se obtiver uma mensagem sobre a revisão das alterações para a solução, clique em **OK**. Se obtiver uma mensagem sobre a aceitação de licença, clique em **Aceito**.

1. Selecione CTRL + F5 para executar a aplicação. A aplicação é apresentada no browser. 

1. Selecione **Criar Nova** no browser e crie algumas tarefas novas na aplicação de tarefas. Também deve ver as tarefas que criou no [adicionar dados de exemplo](#add-sample-data)

   ![Aplicação de Lista A Fazer com dados de exemplo](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list-preview.png)

Agora pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes dados novos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como criar uma conta do Azure Cosmos DB, criar um contentor e adicionar itens a ela com o Data Explorer e executar uma aplicação web. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).


