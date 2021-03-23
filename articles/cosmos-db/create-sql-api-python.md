---
title: 'Quickstart: Construa uma app Python utilizando a conta API API AZure Cosmos DB SQL'
description: Apresenta um exemplo de código Python que pode utilizar para ligar e consultar a Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: fee0591622c1ee07b6e954b3cadc208a300ab6a5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798787"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Quickstart: Construa uma aplicação Python utilizando uma conta API AZure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK v4 de Java](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste quickstart, você cria e gere uma conta API API AZURE Cosmos DB SQL a partir do portal Azure, e do Código do Estúdio Visual com uma aplicação Python clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de Cosmos DB. As suas opções são:
    * Dentro de uma subscrição ativa do Azure:
        * [Crie uma Conta Gratuita Azure](https://azure.microsoft.com/free) ou use a subscrição existente 
        * [Créditos mensais do Estúdio Visual](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB Free Tier](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Sem uma subscrição ativa do Azure:
        * [Experimente gratuitamente Azure Cosmos DB,](https://azure.microsoft.com/try/cosmosdb/)um ambiente de testes que dura 30 dias.
        * [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 ou 3.6+](https://www.python.org/downloads/), com o `python` executável no seu `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão Python para Código de Estúdio Visual.](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)
- [Git.](https://www.git-scm.com/downloads) 
- [Azure Cosmos DB SQL API SDK para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contentor

Pode agora utilizar a ferramenta Data Explorer no portal Azure para criar uma base de dados e um recipiente. 

1. Selecione **data explorer**  >  **novo recipiente**. 
    
    A área **do Recipiente Adicionar** é apresentada na extrema direita, pode ser necessário deslocar-se para a frente para o ver.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="O Data Explorer no portal do Azure, painel Adicionar Contentor":::

2. Na página **do recipiente Adicionar,** introduza as definições para o novo recipiente.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|Tarefas|Designe a nova base de dados como *Tarefas*. Os nomes da base de dados devem conter de 1 a 255 caracteres, não podendo conter `/, \\, #, ?` , ou um espaço de fuga. Consulte a opção **de produção da base de dados Provision,** que lhe permite partilhar o produto que está previsto na base de dados em todos os contentores da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a produção a 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar verticalmente o débito mais tarde.| 
    |**ID do Contentor**|Itens|*Insira os Itens* como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Chave de partição**| /categoria| A amostra descrita neste artigo *utiliza/categoria* como chave de partição.|
    
    Além das definições anteriores, pode opcionalmente adicionar **teclas Únicas** para o recipiente. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política chave única ao criar um recipiente, garante a singularidade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).
    
    Selecione **OK**. O Data Explorer mostra a base de dados e o contentor novos.

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação API SQL a partir do GitHub, definir a cadeia de ligação e executá-la. Este quickstart utiliza a versão 4 do [Python SDK](https://pypi.org/project/azure-cosmos/#history).

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```cmd
    md "git-samples"
    ```
   Se estiver a utilizar um pedido de pancada, deve utilizar o seguinte comando:

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

1. Na sua conta DB Azure Cosmos no [portal Azure,](https://portal.azure.com/)selecione **Keys** a partir da navegação à esquerda. Utilize os botões de cópia no lado direito do ecrã para copiar a **URI** e a **Chave Primária** para o ficheiro *cosmos_get_started.py* no passo seguinte.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Obtenha uma chave de acesso e URI nas definições de Chaves no portal Azure":::

2. No Código do Estúdio Visual, abra o ficheiro *cosmos_get_started.py* em *\git-samples\azure-cosmos-db-python-start-start-start.*

3. Copie o seu valor **URI** a partir do portal (utilizando o botão de cópia) e faça com que o valor da variável **ponto final** em *cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Em seguida, copie o seu valor **PRINCIPAL do** portal e faça dele o valor da **chave** em *cosmos_get_started.py*. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `key = 'FILLME'`

5. Guarde o ficheiro *cosmos_get_started.py.*

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Saiba mais sobre os recursos de base de dados criados em código ou avance para atualizar a [sua cadeia de ligação](#update-your-connection-string).

Os seguintes cortes são todos retirados do ficheiro *cosmos_get_started.py.*

* O CosmosClient é inicializado. Certifique-se de atualizar os valores "ponto final" e "chave" descritos na secção [de cadeia de ligação Atualização.](#update-your-connection-string) 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* É criada uma nova base de dados.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* É criado um novo contentor, com 400 RU/s de [produção a provisionada](request-units.md). Escolhemos `lastName` como chave de [partição,](partitioning-overview.md#choose-partitionkey)o que nos permite fazer consultas eficientes que filtram esta propriedade. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Alguns itens são adicionados ao contentor. Os contentores são uma coleção de itens (documentos JSON) que podem ter esquemas variados. Os métodos de ```get_[name]_family_item``` ajuda devolvem representações de uma família que está armazenada em Azure Cosmos DB como documentos JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* As leituras de pontos (as principais procuras de valor) são realizadas utilizando o `read_item` método. Imprimimos a [carga RU](request-units.md) de cada operação.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Uma consulta é realizada usando a sintaxe de consulta SQL. Como estamos a usar valores-chave de partição ```lastName``` na cláusula WHERE, a Azure Cosmos DB irá encaminhar eficientemente esta consulta para as divisórias relevantes, melhorando o desempenho.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Executar a aplicação

1. No Código do Estúdio Visual, selecione  >  **'View Command Palette'.** 

2. Na linha de comandos, introduza **Python: Selecione o Interpretador** e, em seguida, selecione a versão do Python a utilizar.

    O Rodapé no Visual Studio Code é atualizado para indicar o interpretador selecionado. 

3. Selecione **Ver**  >  **Terminal Integrado** para abrir o terminal integrado do Código do Estúdio Visual.

4. Na janela do terminal integrado, certifique-se de que está na pasta *azure-cosmos-db-python-start-starter.* Caso contrário, execute o seguinte comando para mudar para a pasta de exemplo. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Execute o seguinte comando para instalar o pacote azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Se obtiver um erro de acesso recusado quando tentar instalar o azure-cosmos, terá de [executar o VS Code como administrador](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Executar o seguinte comando para executar a amostra e criar e armazenar novos documentos em Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Para confirmar que os novos itens foram criados e guardados, no portal Azure, selecione **Data Explorer**  >  **AzureSampleFamilyDatabase**  >  **Itens**. Veja os itens que foram criados. Por exemplo, aqui está uma amostra do documento JSON para a família Andersen:
   
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

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta DB Azure Cosmos, criar um recipiente usando o Data Explorer e executar uma aplicação Python no Código do Estúdio Visual. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados para o Azure Cosmos DB para a SQL API](import-data.md)
