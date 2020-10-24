---
title: Construir um Quadro Azure Cosmos DB .NET, aplicação core utilizando a API Gremlin
description: Apresenta um exemplo de código .NET Framework/Core que pode utilizar para ligar e consultar o Azure Cosmos DB
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: jasonh
ms.custom: devx-track-dotnet
ms.openlocfilehash: 0b81bf951df6db0da7ed049c435a319f6ee33b8a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479056"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Quickstart: Construir uma aplicação .NET Framework ou Core utilizando a conta API API AZure Cosmos DB Gremlin

> [!div class="op_single_selector"]
> * [Consola do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este quickstart demonstra como criar uma conta API AZure Cosmos DB [Gremlin,](graph-introduction.md) base de dados e gráfico (recipiente) utilizando o portal Azure. Em seguida, irá criar e executar uma aplicação de consola com o controlador open-source [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2019 instalado, **free** pode baixar e utilizar a [Edição Comunitária visual 2019 gratuita do Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação da API do Gremlin a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Em seguida, abra o Visual Studio e o ficheiro da solução.

5. Restaure os pacotes NuGet no projeto. Isto deve incluir o controlador Gremlin.Net, bem como o pacote Newtonsoft.Json.


6. Também pode instalar manualmente o controlador Gremlin.Net com o gestor de pacotes Nuget ou o [utilitário de linha de comandos do nuget](/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Os seguintes fragmentos são retirados do ficheiro Program.cs.

* Desaça os parâmetros de ligação com base na conta acima criada: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* Os comandos gremlin a serem executados estão listados num Dicionário:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Criar um objeto novo `GremlinServer` e `GremlinClient` de ligação utilizando os parâmetros acima indicados:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Execute cada consulta gremlin usando o `GremlinClient` objeto com uma tarefa assídua. Pode ler as consultas de Gremlin a partir do dicionário definido no passo anterior e executá-las. Mais tarde obtenha o resultado e leia os valores, que são formatados como um dicionário, usando a `JsonSerializer` classe de Newtonsoft.Jsem pacote:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. A partir do [portal do Azure](https://portal.azure.com/), navegue até à sua conta da base de dados de gráficos. No separador **Descrição geral**, pode ver dois pontos finais- 
 
   **.NET SDK URI** - Este valor é utilizado quando se liga à conta de gráfico utilizando a biblioteca Microsoft.Azure.Graphs. 

   **Ponto final do Gremlin** – Este valor é utilizado quando se liga à conta de gráficos através da biblioteca de Gremlin.Net.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Copiar o ponto final&quot;:::

   Para executar esta amostra, copie o valor **gremlin Endpoint,** elimine o número de porta no final, ou seja, o URI torna-se `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` . O valor do ponto final deve parecer `testgraphacct.gremlin.cosmosdb.azure.com`

1. Em seguida, navegue no separador **Chaves** e copie o valor **KEY PRINCIPAL** a partir do portal Azure. 

1. Depois de copiar o URI e a CHAVE PRIMÁRIA da sua conta, guarde-os para uma nova variável ambiental na máquina local que executa a aplicação. Para definir a variável ambiente, abra uma janela de ordem de comando e execute o seguinte comando. Certifique-se de que substitui os valores <Your_Azure_Cosmos_account_URI> e <Your_Azure_Cosmos_account_PRIMARY_KEY>.

   ```console
   setx Host &quot;<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com&quot;
   setx PrimaryKey &quot;<Your_Azure_Cosmos_account_PRIMARY_KEY>&quot;
   ```

1. Abra o ficheiro *Program.cs* e atualize as variáveis &quot;base de dados e contentores" com os nomes da base de dados e do recipiente (que também é o nome do gráfico) criado acima.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Guarde o ficheiro Program.cs. 

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Executar a aplicação de consola

Clique em CTRL + F5 para executar a aplicação. A aplicação irá imprimir os comandos de consulta Gremlin e os resultados na consola.

   A janela de consola apresenta os vértices e as margens a adicionar ao gráfico. Quando o script tiver concluído, prima ENTER para fechar a janela da consola.

## <a name="browse-using-the-data-explorer"></a>Utilizar o Data Explorer para pesquisar

Agora, pode voltar ao Data Explorer no portal do Azure e procurar e consultar os dados do gráfico novo.

1. No Data Explorer, a nova base de dados é apresentada no painel Gráficos. Expanda a base de dados e os nós do contentor e, em seguida, clique em **Gráfico**.

2. Clique no botão **Aplicar Filtro** para utilizar a consulta predefinida para ver todos os vértices no gráfico. Os dados gerados pela aplicação de exemplo são apresentados no painel Gráficos.

    Pode ampliar e reduzir o gráfico, expandir o espaço de visualização do gráfico, adicionar mais vértices e mover vértices na superfície de visualização.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Copiar o ponto final&quot;:::

   Para executar esta amostra, copie o valor **gremlin Endpoint,** elimine o número de porta no final, ou seja, o URI torna-se `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` . O valor do ponto final deve parecer `testgraphacct.gremlin.cosmosdb.azure.com`

1. Em seguida, navegue no separador **Chaves** e copie o valor **KEY PRINCIPAL** a partir do portal Azure. 

1. Depois de copiar o URI e a CHAVE PRIMÁRIA da sua conta, guarde-os para uma nova variável ambiental na máquina local que executa a aplicação. Para definir a variável ambiente, abra uma janela de ordem de comando e execute o seguinte comando. Certifique-se de que substitui os valores <Your_Azure_Cosmos_account_URI> e <Your_Azure_Cosmos_account_PRIMARY_KEY>.

   ```console
   setx Host &quot;<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com&quot;
   setx PrimaryKey &quot;<Your_Azure_Cosmos_account_PRIMARY_KEY>&quot;
   ```

1. Abra o ficheiro *Program.cs* e atualize as variáveis &quot;base de dados e contentores":::

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)