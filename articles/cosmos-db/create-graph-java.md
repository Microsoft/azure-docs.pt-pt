---
title: Construir uma base de dados de gráficos com Java em Azure Cosmos DB
description: Apresenta um exemplo de código Java que pode utilizar para ligar e consultar dados de gráficos no Azure Cosmos DB com Gremlin.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: jasonh
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2ae86f918b1ecaf28d5f0118d90ea6c2389674da
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129716"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Quickstart: Construir uma base de dados de gráficos com o Java SDK e o API API AZure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Consola do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Neste quickstart, você cria e gere uma conta API AZure Cosmos DB Gremlin (gráfico) a partir do portal Azure, e adiciona dados usando uma aplicação Java clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Kit de Desenvolvimento de Java (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a `JAVA_HOME` variável ambiente para a pasta onde o JDK está instalado.
- Um [arquivo binário de Maven.](https://maven.apache.org/download.cgi) 
- [Git.](https://www.git-scm.com/downloads) 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de gráficos, tem de criar uma conta de base de dados do Gremlin (Gráfico) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação API do Gremlin a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente.  

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma pasta e instalar a aplicação de exemplo.  

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-information).

Os seguintes excertos são todos retirados do ficheiro *C:\git-samples\azure-cosmos-db-java-getting-started\src\GetStarted\Program.java.*

Esta aplicação de consola Java utiliza uma base de dados [API gremlin](graph-introduction.md) com o condutor do OSS [Apache TinkerPop.](https://tinkerpop.apache.org/) 

- O Gremlin `Client` é inicializado a partir da configuração no ficheiro *C:\git-samples\azure-cosmos-db-java-getting-started\src\remote.yaml.*

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- São executadas séries de passos do Gremlin com o método `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Atualizar as informações da ligação

Agora, regresse ao portal do Azure para obter as informações da ligação e copie-as para a aplicação. Estas definições permitem à aplicação comunicar com a base de dados alojada.

1. Na sua conta DB Azure Cosmos no [portal Azure,](https://portal.azure.com/)selecione **Keys** . 

    Copie a primeira parte do valor do URI.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::

2. Abra o ficheiro *src/remote.yaml* e cole o valor de ID único `$name$` em `hosts: [$name$.graphs.azure.com]` .

    Linha 1 de *remote.yaml* deve agora parecer semelhante a 

    `hosts: [test-graph.graphs.azure.com]`

3. Altere `graphs` para `gremlin.cosmosdb` no valor `endpoint`. (Se tiver criado a conta da base de dados do gráfico antes de 20 de dezembro de 2017, não faça alterações ao valor do ponto final e avance para o passo seguinte.)

    O valor de ponto final deverá agora ter o seguinte aspeto:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. No portal do Azure, utilize o botão de cópia para copiar a CHAVE PRIMÁRIA e cole-a em `$masterKey$` no `password: $masterKey$`.

    Linha 4 de *remote.yaml* deve agora parecer semelhante a 

    `password: 2Ggkr662ifxz2Mg==`

5. Alterar a linha 3 de *remote.yaml* de

    `username: /dbs/$database$/colls/$collection$`

    para 

    `username: /dbs/sample-database/colls/sample-graph`

    Se utilizou um nome único no seu gráfico ou base de dados de exemplo, atualize os valores conforme adequado.

6. Guarde o ficheiro *remote.yaml.*

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Na janela de terminal do git, `cd` na pasta azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Na janela de terminal do git, utilize o seguinte comando para instalar os pacotes Java necessários.

   ```git
   mvn package
   ```

3. Na janela de terminal do git, utilize o seguinte comando para iniciar a aplicação do Java.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    A janela de terminal apresenta os vértices a adicionar ao gráfico. 
    
    Se ocorrerem erros de tempo limite, verifique se atualizou as informações de ligação corretamente em [Atualizar as suas informações de ligação](#update-your-connection-information) e tente executar o último comando novamente. 
    
    Assim que o programa parar, selecione Enter e, em seguida, volte para o portal Azure no seu navegador de Internet. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Rever e adicionar dados de exemplo

Agora, pode voltar ao Data Explorer e ver os vértices adicionados ao gráfico e adicionar mais pontos de dados.

1. Na sua conta DB Azure Cosmos no portal Azure, selecione **Data Explorer,** expanda **o gráfico de amostras,** selecione **Graph** , e, em seguida, selecione **Apply Filter** . 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::

2. Na lista **Resultados** , repare nos utilizadores novos que são adicionados ao gráfico. Selecione **ben** e repare que o utilizador está ligado a robin. Pode mover os vértices ao redor ao arrastar e largar, ampliar e reduzir ao deslocar a roda do rato e expandir o tamanho do gráfico com a seta dupla. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::

3. Vamos adicionar alguns utilizadores novos. Selecione **Novo Vértex** para adicionar dados ao seu gráfico.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::

4. Na caixa da etiqueta, introduza *person* .

5. **Selecione Adicionar propriedade** para adicionar cada uma das seguintes propriedades. Tenha em atenção que pode criar propriedades exclusivas para cada pessoa no seu gráfico. Só é necessária a chave de id.

    key|valor|Notas
    ----|----|----
    ID|ashley|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|feminino| 
    técnico | java | 

    > [!NOTE]
    > Neste guia de introdução, criará uma coleção não particionada. No entanto, se criar uma coleção particionada mediante a especificação de uma chave de partição durante a criação da coleção, terá de incluir a chave da partição como uma chave em cada vértice novo. 

6. Selecione **OK** . Poderá ter de expandir o ecrã para ver **OK** , na parte inferior.

7. Selecione **Novo Vertex** novamente e adicione um novo utilizador adicional. 

8. Introduza uma etiqueta de *pessoa* .

9. **Selecione Adicionar propriedade** para adicionar cada uma das seguintes propriedades:

    key|valor|Notas
    ----|----|----
    ID|rakesh|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|masculino| 
    escola|MIT| 

10. Selecione **OK** . 

11. Selecione o botão **'Aplicar filtro'** com o filtro predefinido `g.V()` para visualizar todos os valores do gráfico. Todos os utilizadores aparecem agora na lista **Resultados** . 

    À medida que adiciona mais dados, pode utilizar filtros para limitar os resultados. Por predefinição, o Data Explorer utiliza o `g.V()` para obter todos os vértices num gráfico. Pode alterá-lo para uma [consulta de gráfico](tutorial-query-graph.md) diferente, como `g.V().count()`, para devolver uma contagem de todos os vértices no gráfico no formato JSON. Se tiver mudado o filtro, volte a alterar o filtro `g.V()` e selecione **Apply Filter** para exibir todos os resultados novamente.

12. Agora, pode ligar rakesh e ashley. Certifique-se de que a **ashley** está selecionada na lista **de Resultados** e, em seguida, selecione :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::  ao lado de **Targets** no lado inferior direito. Poderá ter de alargar a janela para ver o botão.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::

13. Na caixa **Target** *introduza rakesh* , e na caixa de **etiquetas Edge** insira *sabe* - e, em seguida, selecione a caixa de verificação.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::

14. Agora, selecione **rakesh** na lista de resultados e veja se ashley e rakesh estão ligados. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Ver e copiar uma chave de acesso no portal do Azure, página Chaves":::

Esta ação conclui a parte da criação de recursos deste tutorial. Pode continuar a adicionar vértices ao seu gráfico, modificar os vértices existentes ou alterar as consultas. Agora vamos rever as métricas que o Azure Cosmos DB oferece e, em seguida, limpar os recursos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta DB Azure Cosmos, criar um gráfico utilizando o Data Explorer e executar uma aplicação Java que adiciona dados ao gráfico. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)

