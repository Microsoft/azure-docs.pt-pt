---
title: 'Início rápido: compilar um aplicativo Python usando Azure Cosmos DB conta da API do SQL'
description: Apresenta um exemplo de código Python que pode utilizar para ligar e consultar a Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: a794a9ed35cbbdd36c2cf136b8afc208c3ea0692
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549019"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Início rápido: criar um aplicativo Python usando uma conta de API do SQL Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Este início rápido demonstra como criar uma conta [API SQL](sql-api-introduction.md) do Azure Cosmos DB, bases de dados de documentos e contentores com o portal do Azure. Em seguida, irá criar e executar uma aplicação de consola com o SDK de Python para a [API SQL](sql-api-sdk-python.md).

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Você pode criar e consultar rapidamente documentos, chave/valor, colunas largas e bancos de dados de gráficos. Todas essas operações se beneficiam da distribuição e da escala de Azure Cosmos DB.

Este guia de início rápido usa a versão 4 do [SDK do Python](https://pypi.org/project/azure-cosmos/#history).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6 +](https://www.python.org/downloads/), com o `python` executável disponível em seu `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensão do Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contentor

Agora você pode usar a ferramenta Data Explorer no portal do Azure para criar um banco de dados e um contêiner. 

1. Selecione **Data Explorer** > **Novo Contentor**. 
    
    A área **Adicionar contêiner** é exibida na extrema direita, talvez seja necessário rolar para a direita para vê-la.

    ![O Data Explorer no portal do Azure, painel Adicionar Contentor](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. Na página **Adicionar contêiner** , insira as configurações para o novo contêiner.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|Tarefas|Insira *ToDoList* como o nome do novo banco de dados. Nomes de base de dados tem de conter entre 1 e 255 carateres e não podem conter `/, \\, #, ?`, ou um espaço à direita. Marque a opção **provisionar taxa de transferência do banco de dados** , que permite que você compartilhe a taxa de transferência provisionada para o banco de dados em todos os contêineres no banco de dados. Essa opção também ajuda na economia de custos. |
    |**Débito**|400|Deixe a taxa de transferência em 400 unidades de solicitação por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    |**ID do Contentor**|Itens|Insira os *itens* como o nome do novo contêiner. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Chave de partição**| /categoria| O exemplo descrito neste artigo usa */Category* como a chave de partição.|
    
    Além das configurações anteriores, você pode opcionalmente adicionar **chaves exclusivas** para o contêiner. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política de chave exclusiva ao criar um contêiner, você garante a exclusividade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).
    
    Selecione **OK**. O Data Explorer mostra a base de dados e o contentor novos.

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação API SQL a partir do GitHub, definir a cadeia de ligação e executá-la.

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```cmd
    md "git-samples"
    ```
   Se você estiver usando um prompt do bash, deverá usar o seguinte comando:

   ```bash
   mkdir "git-samples"
   ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. Na [portal do Azure](https://portal.azure.com/), na sua conta do cosmos do Azure, no painel de navegação esquerdo, selecione **chaves**. Irá utilizar os botões de copiar no lado direito do ecrã para copiar o **URI** e a **Chave Primária** para o ficheiro `cosmos_get_started.py` no passo seguinte.

    ![Obter uma chave de acesso e um URI nas configurações de chaves no portal do Azure](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Abra o arquivo `cosmos_get_started.py` no \git-samples\azure-Cosmos-DB-Python-Getting-Started no Visual Studio Code.

3. Copie o valor do **URI** do portal (usando o botão de cópia) e torne-o o valor da variável de **ponto de extremidade** em ``cosmos_get_started.py``. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Em seguida, copie o valor da **chave primária** do portal e transforme-o no valor da **chave** em ``cosmos_get_started.py``. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `key = 'FILLME'`

5. Guarde o ficheiro ``cosmos_get_started.py``.

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Saiba mais sobre os recursos do banco de dados criados no código ou pule para [atualizar sua cadeia de conexão](#update-your-connection-string).

Os seguintes fragmentos foram todos retirados do ficheiro `cosmos_get_started.py`.

* O CosmosClient é inicializado. Certifique-se de atualizar os valores de "ponto de extremidade" e "chave", conforme descrito na seção [atualizar a cadeia de conexão](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* É criada uma nova base de dados.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Um novo contêiner é criado, com 400 RU/s de [taxa de transferência provisionada](request-units.md). Escolhemos `lastName` como a [chave de partição](partitioning-overview.md#choose-partitionkey), o que nos permite fazer consultas eficientes que filtram essa propriedade. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Alguns itens são adicionados ao contentor. Contêineres são uma coleção de itens (documentos JSON) que podem ter um esquema variado. Os métodos auxiliares ```get_[name]_family_item``` retornam representações de uma família que são armazenadas em Azure Cosmos DB como documentos JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* As leituras de ponto (pesquisas de valor de chave) são executadas usando o método `read_item`. Imprimimos a [cobrança de ru](request-units.md) de cada operação.
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Uma consulta é executada usando A sintaxe de consulta SQL. Como estamos usando valores de chave de partição de ```lastName``` na cláusula WHERE, Azure Cosmos DB roteará com eficiência essa consulta para as partições relevantes, melhorando o desempenho.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Executar a aplicação

1. No Visual Studio Code, selecione **Vista** > **Paleta de Comando**. 

2. Na linha de comandos, introduza **Python: Selecione o Interpretador** e, em seguida, selecione a versão do Python a utilizar.

    O Rodapé no Visual Studio Code é atualizado para indicar o interpretador selecionado. 

3. Selecione **Ver** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.

4. Na janela do terminal integrado, certifique-se de que está na pasta azure-cosmos-db-python-getting-started. Caso contrário, execute o seguinte comando para mudar para a pasta de exemplo. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Execute o seguinte comando para instalar o pacote azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Se obtiver um erro de acesso recusado quando tentar instalar o azure-cosmos, terá de [executar o VS Code como administrador](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Execute o comando a seguir para executar o exemplo e criar e armazenar novos documentos no Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Para confirmar se os novos itens foram criados e salvos, na portal do Azure, selecione **Data Explorer** > **AzureSampleFamilyDatabase** > **itens**. Exiba os itens que foram criados. Por exemplo, aqui está um exemplo de documento JSON para a família Andersen:

```json
{
    "id": "Andersen-1569479288379",
    "lastName": "Andersen",
    "district": "WA5",
    "parents": [
        {
            "familyName": null,
            "firstName": "Thomas"
        },
        {
            "familyName": null,
            "firstName": "Mary Kay"
        }
    ],
    "children": null,
    "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
    },
    "registered": true,
    "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
    "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
    "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
    "_attachments": "attachments/",
    "_ts": 1569479288
}
```

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como criar uma conta do Azure Cosmos, criar um contêiner usando o Data Explorer e executar um aplicativo. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados para o Azure Cosmos DB para a SQL API](import-data.md)


