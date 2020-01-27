---
title: Tutorial – implementar Azure Databricks com um ponto de extremidade Cosmos DB
description: Este tutorial descreve como implementar Azure Databricks em uma rede virtual com um ponto de extremidade de serviço habilitado para Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706154"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Tutorial: implementar Azure Databricks com um ponto de extremidade Cosmos DB

Este tutorial descreve como implementar um ambiente de databricks injetado VNet com um ponto de extremidade de serviço habilitado para Cosmos DB.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um espaço de trabalho Azure Databricks em uma rede virtual
> * Criar um ponto de extremidade de serviço de Cosmos DB
> * Criar uma conta de Cosmos DB e importar dados
> * Criar um cluster Azure Databricks
> * Cosmos DB de consulta de um bloco de anotações Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, faça o seguinte:

* Crie um [espaço de trabalho Azure Databricks em uma rede virtual](quickstart-create-databricks-workspace-vnet-injection.md).

* Baixe o [conector do Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Baixe dados de exemplo dos [NOAA National centers para obter informações ambientais](https://www.ncdc.noaa.gov/stormevents/). Selecione um Estado ou uma área e selecione **Pesquisar**. Na próxima página, aceite os padrões e selecione **Pesquisar**. Em seguida, selecione **download de CSV** no lado esquerdo da página para baixar os resultados.

* Baixe o [binário pré-compilado](https://aka.ms/csdmtool) da ferramenta de migração de dados Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Criar um ponto de extremidade de serviço de Cosmos DB

1. Depois de implantar um espaço de trabalho Azure Databricks em uma rede virtual, navegue até a rede virtual na [portal do Azure](https://portal.azure.com). Observe as sub-redes públicas e privadas que foram criadas por meio da implantação do databricks.

   ![Sub-redes da rede virtual](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selecione a *sub-rede pública* e crie um ponto de extremidade de serviço de Cosmos DB. Em seguida, **salve**.
   
   ![Adicionar um ponto de extremidade de serviço de Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Criar uma conta do Cosmos DB

1. Abra o portal do Azure. No lado superior esquerdo da tela, selecione **criar um recurso > bancos de dados > Azure Cosmos DB**.

2. Preencha os **detalhes da instância** na guia **noções básicas** com as seguintes configurações:

   |Definição|Valor|
   |-------|-----|
   |Subscrição|*a sua subscrição*|
   |Grupo de Recursos|*seu grupo de recursos*|
   |Nome da Conta|DB-vnet-serviço-ponto de extremidade|
   |API|Núcleo (SQL)|
   |Localização|E.U.A. Oeste|
   |Redundância geográfica|Desativar|
   |Gravações de várias regiões|Ativar|

   ![Adicionar um ponto de extremidade de serviço de Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selecione a guia **rede** e configure sua rede virtual. 

   a. Escolha a rede virtual que você criou como um pré-requisito e, em seguida, selecione *Public-subnet*. Observe que a *sub-rede privada* tem a observação *"o ponto de extremidade do Microsoft AzureCosmosDB" está faltando "* . Isso ocorre porque você habilitou apenas o ponto de extremidade de serviço Cosmos DB na *sub-rede pública*.

   b. Verifique se você tem **permissão de acesso de portal do Azure** habilitado. Essa configuração permite que você acesse sua conta de Cosmos DB do portal do Azure. Se essa opção for definida como **negar**, você receberá erros ao tentar acessar sua conta. 

   > [!NOTE]
   > Não é necessário para este tutorial, mas você também pode habilitar o recurso *permitir acesso do meu IP* se quiser ter a capacidade de acessar sua conta de Cosmos DB de seu computador local. Por exemplo, se você estiver se conectando à sua conta usando o SDK do Cosmos DB, você precisará habilitar essa configuração. Se estiver desabilitado, você receberá erros de "acesso negado".

   ![Cosmos DB configurações de rede da conta](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selecione **revisar + criar**e **criar** para criar sua conta de Cosmos DB dentro da rede virtual.

5. Quando sua conta de Cosmos DB tiver sido criada, navegue até **chaves** em **configurações**. Copie a cadeia de conexão primária e salve-a em um editor de texto para uso posterior.

    ![Página de chaves de conta Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selecione **Data Explorer** e **nova coleção** para adicionar um novo banco de dados e uma coleção à sua conta de Cosmos DB.

    ![Cosmos DB nova coleção](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Carregar dados para Cosmos DB

1. Abra a versão de interface gráfica da [ferramenta de migração de dados para Cosmos DB](https://aka.ms/csdmtool), **Dtui. exe**.

    ![Ferramenta de Migração de Dados do Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na guia **informações de origem** , selecione **arquivo (s) CSV** na lista suspensa **importar de** . Em seguida, selecione **Adicionar arquivos** e adicione o CSV de dados do Storm baixado como um pré-requisito.

    ![Informações de origem da ferramenta de migração de dados Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na guia **informações de destino** , insira a cadeia de conexão. O formato da cadeia de conexão é `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. O AccountEndpoint e o AccountKey são incluídos na cadeia de conexão primária que você salvou na seção anterior. Acrescente `Database=<your database name>` ao final da cadeia de conexão e selecione **verificar**. Em seguida, adicione o nome da coleção e a chave de partição.

    ![Informações de destino da ferramenta de migração de dados Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selecione **Avançar** até chegar à página Resumo. Em seguida, selecione **importar**.

## <a name="create-a-cluster-and-add-library"></a>Criar um cluster e Adicionar biblioteca

1. Navegue até o serviço de Azure Databricks na [portal do Azure](https://portal.azure.com) e selecione **Iniciar espaço de trabalho**.

   ![Iniciar o espaço de trabalho do databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Crie um novo cluster. Escolha um nome de cluster e aceite as configurações padrão restantes.

   ![Novas configurações de cluster](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Depois que o cluster for criado, navegue até a página do cluster e selecione a guia **bibliotecas** . Selecione **instalar novo** e carregar o arquivo JAR do conector do Spark para instalar a biblioteca.

    ![Instalar a biblioteca do conector do Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Você pode verificar se a biblioteca foi instalada na guia **bibliotecas** .

    ![Guia bibliotecas de cluster do databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Cosmos DB de consulta de um bloco de anotações do databricks

1. Navegue até o espaço de trabalho Azure Databricks e crie um novo notebook Python.

    ![Criar novo bloco de anotações do databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Execute o seguinte código Python para definir a configuração de conexão Cosmos DB. Altere o **ponto de extremidade**, **MasterKey**, **banco de dados**e **coleção** de acordo.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Use o código Python a seguir para carregar os dados e criar uma exibição temporária.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Use o comando mágico a seguir para executar uma instrução SQL que retorna dados.

    ```python
    %sql
    select * from storm
    ```

    Você conectou com êxito o espaço de trabalho do databricks inseridos por VNet a um ponto de extremidade de serviço habilitado Cosmos DB recurso. Para ler mais sobre como se conectar ao Cosmos DB, consulte [conector de Azure Cosmos DB para Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos, o espaço de trabalho Azure Databricks e todos os recursos relacionados. A exclusão do trabalho evita a cobrança desnecessária. Se estiver planejando usar o espaço de trabalho Azure Databricks no futuro, você poderá interromper o cluster e reiniciá-lo mais tarde. Se você não pretende usar este Azure Databricks espaço de trabalho, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda na portal do Azure, clique em grupos de **recursos** e, em seguida, clique no nome do grupo de recursos que você criou.

2. Na página do grupo de recursos, selecione **excluir**, digite o nome do recurso a ser excluído na caixa de texto e, em seguida, selecione **excluir** novamente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você implantou um espaço de trabalho Azure Databricks em uma rede virtual e usou o conector Cosmos DB Spark para consultar dados de Cosmos DB do databricks. Para saber mais sobre como trabalhar com Azure Databricks em uma rede virtual, continue no tutorial para usar o SQL Server com Azure Databricks.

> [!div class="nextstepaction"]
> [Tutorial: consultar um contêiner SQL Server do Docker do Linux em uma rede virtual de um notebook Azure Databricks](vnet-injection-sql-server.md)
