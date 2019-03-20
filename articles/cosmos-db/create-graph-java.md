---
title: Criar uma base de dados do gráfico do Azure Cosmos DB com Java
description: Apresenta um exemplo de código Java que pode utilizar para ligar e consultar dados de gráficos no Azure Cosmos DB com Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: lbosq
ms.openlocfilehash: db931a3a114be101eeb5e14fa7ba100a789e7cf5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094996"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-the-java-sdk"></a>Início rápido: Criar uma base de dados no Azure Cosmos DB com o SDK de Java 

> [!div class="op_single_selector"]
> * [Consola do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Com o Azure Cosmos DB, pode criar e consultar rapidamente documentos geridos, tabelas e bases de dados de gráfico. 

Este guia de início rápido cria uma base de dados de gráficos simples com as ferramentas do portal do Azure para o Azure Cosmos DB. Este início rápido também lhe mostra como criar rapidamente uma aplicação de consola Java com uma base de dados [Gremlin API](graph-introduction.md) com o controlador OSS [Apache TinkerPop](https://tinkerpop.apache.org/). As instruções deste guia de início rápido podem ser seguidas em qualquer sistema operativo capaz de executar Java. Esta início rápido irá familiarizá-lo-á com a criação e modificação de gráficos na IU ou programaticamente, conforme a sua preferência. 

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Além disso:

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário [Maven](https://maven.apache.org/)
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de gráficos, tem de criar uma conta de base de dados do Gremlin (Gráfico) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

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

Os fragmentos seguintes são retirados do ficheiro C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java.

* O Gremlin `Client` é iniciado a partir da configuração no ficheiro C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* São executadas séries de passos do Gremlin com o método `client.submit`.

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

1. No [portal do Azure](https://portal.azure.com/), clique em **Chaves**. 

    Copie a primeira parte do valor do URI.

    ![Ver e copiar uma chave de acesso no portal do Azure, página Chaves](./media/create-graph-java/keys.png)
2. Abra o ficheiro src/remote.yaml e cole o valor de ID único em `$name$` no `hosts: [$name$.graphs.azure.com]`.

    A Linha 1 do remote.yaml deve agora ter um aspeto semelhante a 

    `hosts: [test-graph.graphs.azure.com]`

3. Altere `graphs` para `gremlin.cosmosdb` no valor `endpoint`. (Se tiver criado a conta da base de dados do gráfico antes de 20 de dezembro de 2017, não faça alterações ao valor do ponto final e avance para o passo seguinte.)

    O valor de ponto final deverá agora ter o seguinte aspeto:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. No portal do Azure, utilize o botão de cópia para copiar a CHAVE PRIMÁRIA e cole-a em `$masterKey$` no `password: $masterKey$`.

    A Linha 4 do remote.yaml deve agora ter um aspeto semelhante a 

    `password: 2Ggkr662ifxz2Mg==`

5. Altere a linha 3 do remote.yaml do

    `username: /dbs/$database$/colls/$collection$`

    para 

    `username: /dbs/sample-database/colls/sample-graph`

    Se utilizou um nome único no seu gráfico ou base de dados de exemplo, atualize os valores conforme adequado.

6. Guarde o ficheiro remote.yaml.

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Na janela de terminal do git, `cd` na pasta azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Na janela de terminal do git, utilize o seguinte comando para instalar os pacotes Java necessários.

   ```
   mvn package
   ```

3. Na janela de terminal do git, utilize o seguinte comando para iniciar a aplicação do Java.
    
    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    A janela de terminal apresenta os vértices a adicionar ao gráfico. 
    
    Se ocorrerem erros de tempo limite, verifique se atualizou as informações de ligação corretamente em [Atualizar as suas informações de ligação](#update-your-connection-information) e tente executar o último comando novamente. 
    
    Assim que o programa parar, prima Enter e, em seguida, mude novamente para o portal do Azure no browser. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Rever e adicionar dados de exemplo

Agora, pode voltar ao Data Explorer e ver os vértices adicionados ao gráfico e adicionar mais pontos de dados.

1. Clique em **Data Explorer**, expanda **sample-graph**, clique em **Gráfico** e clique em **Aplicar Filtro**. 

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. Na lista **Resultados**, repare nos utilizadores novos que são adicionados ao gráfico. Selecione **ben** e repare que o utilizador está ligado a robin. Pode mover os vértices ao redor ao arrastar e largar, ampliar e reduzir ao deslocar a roda do rato e expandir o tamanho do gráfico com a seta dupla. 

   ![Vértices novos no gráfico no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Vamos adicionar alguns utilizadores novos. Clique no botão **Vértice Novo** para adicionar os dados ao gráfico.

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Na caixa da etiqueta, introduza *person*.

5. Clique em **Adicionar propriedade** para adicionar cada uma das seguintes propriedades. Tenha em atenção que pode criar propriedades exclusivas para cada pessoa no seu gráfico. Só é necessária a chave de id.

    key|valor|Notas
    ----|----|----
    ID|ashley|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|feminino| 
    técnico | java | 

    > [!NOTE]
    > Neste guia de introdução, criará uma coleção não particionada. No entanto, se criar uma coleção particionada mediante a especificação de uma chave de partição durante a criação da coleção, terá de incluir a chave da partição como uma chave em cada vértice novo. 

6. Clique em **OK**. Poderá ter de expandir o ecrã para ver **OK**, na parte inferior.

7. Clique em **Vértice Novo** novamente e adicione outro utilizador. 

8. Introduza uma etiqueta de *pessoa*.

9. Clique em **Adicionar propriedade** para adicionar cada uma das seguintes propriedades:

    key|valor|Notas
    ----|----|----
    ID|rakesh|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|masculino| 
    escola|MIT| 

10. Clique em **OK**. 

11. Clique no botão **Aplicar Filtro** com o filtro `g.V()` predefinido para apresentar todos os valores no gráfico. Todos os utilizadores aparecem agora na lista **Resultados**. 

    À medida que adiciona mais dados, pode utilizar filtros para limitar os resultados. Por predefinição, o Data Explorer utiliza o `g.V()` para obter todos os vértices num gráfico. Pode alterá-lo para uma [consulta de gráfico](tutorial-query-graph.md) diferente, como `g.V().count()`, para devolver uma contagem de todos os vértices no gráfico no formato JSON. Se tiver alterado o filtro, altere o filtro de volta para `g.V()` e clique em **Aplicar Filtro** para apresentar todos os resultados novamente.

12. Agora, pode ligar rakesh e ashley. Confirme que **ashley** está selecionada na lista **Resultados** e clique no botão ![Alterar o destino de um vértice num gráfico](./media/create-graph-java/edit-pencil-button.png)  junto a **Destinos** no lado inferior direito. Poderá ter de alargar a janela para ver o botão.

    ![Alterar o destino de um vértice de um gráfico](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. Na caixa **Destino**, escreva *rakesh*, na caixa **Etiqueta da extremidade**, escreva *conhece* e, em seguida, clique na caixa de verificação.

    ![Adicionar uma ligação entre ashley e rakesh no Data Explorer](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Agora, selecione **rakesh** na lista de resultados e veja se ashley e rakesh estão ligados. 

    ![Dois vértices ligados no Data Explorer](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    Esta ação conclui a parte da criação de recursos deste tutorial. Pode continuar a adicionar vértices ao seu gráfico, modificar os vértices existentes ou alterar as consultas. Agora vamos rever as métricas que o Azure Cosmos DB oferece e, em seguida, limpar os recursos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)

