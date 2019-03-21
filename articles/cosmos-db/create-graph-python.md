---
title: 'Início rápido: Gremlin API com Python - Azure Cosmos DB'
description: Este início rápido mostra como utilizar a API do Gremlin do Azure Cosmos DB para criar uma aplicação de consola com o portal do Azure e Python
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: lbosq
ms.openlocfilehash: 5b76d7393e78b464227e93a4c7c8a3f22ccc169e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092536"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Início rápido: Criar uma base de dados no Azure Cosmos DB com o Python e o portal do Azure

> [!div class="op_single_selector"]
> * [Consola do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Este início rápido mostra como utilizar Python e a [API do Gremlin](graph-introduction.md) do Azure Cosmos DB para criar uma aplicação de consola através da clonagem de um exemplo do GitHub. Este guia de introdução também o acompanha ao longo da criação de uma conta do Azure Cosmos DB ao utilizar o portal do Azure baseado na Web.   

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a tabela, a chave/valor e as bases de dados de gráficos que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB.  

> [!NOTE]
> Este guia de introdução exige uma conta de base de dados de gráficos que tenha sido criada numa data posterior a 20 de dezembro de 2017. As contas existentes irão suportar Python depois de serem migradas para disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Em alternativa, pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.

Além disso:
* [Python](https://www.python.org/downloads/) versão 3.5 ou mais recente
* [Gestor de pacotes pip](https://pip.pypa.io/en/stable/installing/)
* [Git](https://git-scm.com/)
* [Controlador de Python para Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python)

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Os fragmentos são obtidos a partir do ficheiro connect.py na pasta C:\git-samples\azure-cosmos-db-graph-python-getting-started\. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-information). 

* O `client` do Gremlin é inicializado na linha 104 em `connect.py`

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* No início do ficheiro `connect.py`, é declarada uma série de passos de Gremlin. Em seguida, são executados com o método `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Atualizar as informações da ligação

Agora, regresse ao portal do Azure para obter as informações da ligação e copie-as para a aplicação. Estas definições permitem à aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](https://portal.azure.com/), clique em **Chaves**. 

    Copie a primeira parte do valor do URI.

    ![Ver e copiar uma chave de acesso no portal do Azure, página Chaves](./media/create-graph-python/keys.png)

2. Abra o ficheiro connect.py e, na linha 104, cole o valor do URI sobre `<YOUR_ENDPOINT>` aqui:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    A parte do URI do objeto de cliente deve agora ter um aspeto semelhante a este código:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Altere o segundo parâmetro do objeto `client` para substituir as cadeias `<YOUR_DATABASE>` e `<YOUR_COLLECTION_OR_GRAPH>`. Se utilizou os valores sugeridos, o parâmetro deve ser semelhante a este código:

    `username="/dbs/sample-database/colls/sample-graph"`

    O objeto `client` completo deverá agora ser semelhante a este código:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. No portal do Azure, utilize o botão de cópia para copiar a CHAVE PRIMÁRIA e cole-a sobre `<YOUR_PASSWORD>` no parâmetro `password=<YOUR_PASSWORD>`.

    A definição do objeto `client` completa deve agora ser semelhante a este código:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Guarde o ficheiro `connect.py`.

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Na janela de terminal do git, `cd` na pasta azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. Na janela de terminal do git, utilize o seguinte comando para instalar os pacotes Python necessários.

   ```
   pip install -r requirements.txt
   ```

3. Na janela de terminal do git, utilize o seguinte comando para iniciar a aplicação Python.
    
    ```
    python connect.py
    ```

    A janela de terminal apresenta os vértices e as extremidades que estão a ser adicionados ao gráfico. 
    
    Se ocorrerem erros de tempo limite, verifique se atualizou as informações de ligação corretamente em [Atualizar as suas informações de ligação](#update-your-connection-information) e tente executar o último comando novamente. 
    
    Assim que o programa parar, prima Enter e, em seguida, mude novamente para o portal do Azure no browser.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Rever e adicionar dados de exemplo

Agora, pode voltar ao Data Explorer e ver os vértices adicionados ao gráfico e adicionar mais pontos de dados.

1. Clique em **Data Explorer**, expanda **sample-graph**, clique em **Gráfico** e clique em **Aplicar Filtro**. 

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. Na lista **Resultados**, repare nos utilizadores novos que são adicionados ao gráfico. Selecione **ben** e repare que está ligado a robin. Pode mover os vértices ao redor ao arrastar e largar, ampliar e reduzir ao deslocar a roda do rato e expandir o tamanho do gráfico com a seta dupla. 

   ![Vértices novos no gráfico no Data Explorer no portal do Azure](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Vamos adicionar alguns utilizadores novos. Clique no botão **Vértice Novo** para adicionar os dados ao gráfico.

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. Introduza uma etiqueta de *pessoa*.

5. Clique em **Adicionar propriedade** para adicionar cada uma das seguintes propriedades. Tenha em atenção que pode criar propriedades exclusivas para cada pessoa no seu gráfico. Só é necessária a chave de id.

    key|valor|Notas
    ----|----|----
    ID|ashley|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|feminino| 
    técnico | java | 

    > [!NOTE]
    > Neste guia de introdução, criámos uma coleção não particionada. No entanto, se criar uma coleção particionada mediante a especificação de uma chave de partição durante a criação da coleção, terá de incluir a chave da partição como uma chave em cada vértice novo. 

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

12. Agora, podemos ligar rakesh e ashley. Confirme que **ashley** está selecionada na lista **Resultados** e clique no botão Editar junto a **Destinos**, no canto inferior direito. Poderá ter de alargar a janela para ver a área **Propriedades**.

    ![Alterar o destino de um vértice de um gráfico](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. Na caixa **Destino**, escreva *rakesh*, na caixa **Etiqueta da extremidade**, escreva *Conhece* e clique no visto.

    ![Adicionar uma ligação entre ashley e rakesh no Data Explorer](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Agora, selecione **rakesh** na lista de resultados e veja se ashley e rakesh estão ligados. 

    ![Dois vértices ligados no Data Explorer](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

    Esta ação conclui a parte da criação de recursos deste tutorial. Pode continuar a adicionar vértices ao seu gráfico, modificar os vértices existentes ou alterar as consultas. Agora vamos rever as métricas que o Azure Cosmos DB oferece e, em seguida, limpar os recursos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)

