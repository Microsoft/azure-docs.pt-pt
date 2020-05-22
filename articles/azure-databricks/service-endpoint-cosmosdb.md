---
title: Tutorial - Implementar tijolos de dados Azure com um ponto final cosmos DB
description: Este tutorial descreve como implementar os Azure Databricks numa rede virtual com um Ponto Final de Serviço habilitado para cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779605"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Tutorial: Implementar tijolos de dados Azure com um ponto final cosmos DB

Este tutorial descreve como implementar um ambiente de Databricks injetado VNet com um ponto final de serviço habilitado para cosmos DB.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um espaço de trabalho Azure Databricks numa rede virtual
> * Criar um ponto final de serviço Cosmos DB
> * Criar uma conta Cosmos DB e importar dados
> * Criar um cluster de Databricks Azure
> * Consulta Cosmos DB de um caderno Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, faça o seguinte:

* Criar um espaço de [trabalho Azure Databricks numa rede virtual.](quickstart-create-databricks-workspace-vnet-injection.md)

* Descarregue o [conector Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Descarregue os dados da amostra dos Centros Nacionais de Informação Ambiental da [NOAA.](https://www.ncdc.noaa.gov/stormevents/) Selecione uma área ou área e selecione **Procurar**. Na página seguinte, aceite as predefinições e selecione **Procurar**. Em seguida, selecione **CSV Baixar** no lado esquerdo da página para baixar os resultados.

* Descarregue o [binário pré-compilado](https://aka.ms/csdmtool) da Ferramenta de Migração de Dados Do BD do Azure Cosmos.

## <a name="create-a-cosmos-db-service-endpoint"></a>Criar um ponto final de serviço Cosmos DB

1. Depois de ter implantado um espaço de trabalho Azure Databricks para uma rede virtual, navegue para a rede virtual no [portal Azure.](https://portal.azure.com) Note as subredes públicas e privadas que foram criadas através da implantação de Databricks.

   ![Subredes de rede virtual](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selecione a *sub-rede pública* e crie um ponto final de serviço Cosmos DB. Em **seguida, guardar**.
   
   ![Adicione um ponto final de serviço Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Criar uma conta do Cosmos DB

1. Abra o portal do Azure. No lado superior esquerdo do ecrã, selecione **Criar um recurso > Bases de dados > Azure Cosmos DB**.

2. Preencha os detalhes da **instância** no separador Basics com as **seguintes** definições:

   |Definição|Valor|
   |-------|-----|
   |Subscrição|*sua assinatura*|
   |Grupo de Recursos|*seu grupo de recursos*|
   |Nome da Conta|db-vnet-service-endpoint|
   |API|Núcleo (SQL)|
   |Localização|E.U.A. Oeste|
   |Redundância Geográfica|Desativar|
   |Escritas de várias regiões|Ativar|

   ![Adicione um ponto final de serviço Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selecione o separador **Rede** e configure a sua rede virtual. 

   a. Escolha a rede virtual que criou como pré-requisito e, em seguida, selecione a *sub-rede pública*. Note que a *sub-rede privada* tem a nota *'Microsoft AzureCosmosDB' ponto final está em falta».* Isto porque só permitiu o ponto final do serviço Cosmos DB na *sub-rede pública.*

   b. Certifique-se de que tem acesso de acesso do **portal Azure** ativado. Esta definição permite-lhe aceder à sua conta Cosmos DB a partir do portal Azure. Se esta opção estiver definida para **Negar,** receberá erros ao tentar aceder à sua conta. 

   > [!NOTE]
   > Não é necessário para este tutorial, mas também pode permitir *o acesso do meu IP* se quiser aceder à sua conta Cosmos DB a partir da sua máquina local. Por exemplo, se estiver a ligar-se à sua conta utilizando o Cosmos DB SDK, tem de ativar esta definição. Se estiver desativado, receberá erros de "Access Negado".

   ![Definições da rede de rede da Conta DB cosmos](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selecione **Review + Create**, e depois **Crie** para criar a sua conta Cosmos DB dentro da rede virtual.

5. Uma vez criada a sua conta Cosmos DB, navegue para **Chaves** em **Definições**. Copie a cadeia de ligação primária e guarde-a num editor de texto para posterior utilização.

    ![Página de chaves de conta Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selecione **Data Explorer** e **New Collection** para adicionar uma nova base de dados e recolha à sua conta Cosmos DB.

    ![Nova coleção cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Enviar dados para Cosmos DB

1. Abra a versão da interface gráfica da ferramenta de migração de [dados para Cosmos DB,](https://aka.ms/csdmtool) **Dtui.exe**.

    ![Ferramenta de Migração de Dados do Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. No separador **Source Information,** selecione **Ficheiros CSV na** **Importação a partir de** dropdown. Em seguida, selecione **Adicionar Ficheiros** e adicionar os dados de tempestade CSV que descarregou como pré-requisito.

    ![Cosmos DB Data Migration Tool fonte de informação](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. No separador **Informação do Alvo,** insera a corda de ligação. O formato de corda de ligação é `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>` . O AccountEndpoint e o AccountKey estão incluídos na cadeia de ligação primária que guardou na secção anterior. Aprete `Database=<your database name>` até ao fim da corda de ligação e selecione **Verificar**. Em seguida, adicione o nome da Coleção e a chave de partição.

    ![Cosmos DB Data Migration Tool target information](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selecione **Em seguida** até chegar à página Resumo. Em seguida, selecione **Import**.

## <a name="create-a-cluster-and-add-library"></a>Criar um cluster e adicionar biblioteca

1. Navegue para o seu serviço Azure Databricks no [portal Azure](https://portal.azure.com) e selecione **Launch Workspace**.

2. Criar um novo aglomerado. Escolha um Nome de Cluster e aceite as definições predefinidas restantes.

   ![Novas configurações de cluster](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Depois de criado o cluster, navegue para a página do cluster e selecione o separador **Bibliotecas.** Selecione **Instalar Novo** e carregue o ficheiro do conector Spark para instalar a biblioteca.

    ![Instalar biblioteca de conector Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Pode verificar se a biblioteca foi instalada no separador **Bibliotecas.**

    ![Separador de bibliotecas de cluster de tijolos de dados](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Consulta Cosmos DB de um caderno databricks

1. Navegue para o seu espaço de trabalho Azure Databricks e crie um novo caderno python.

    ![Criar novo caderno Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Executar o seguinte código python para definir a configuração de ligação Cosmos DB. Alterar o **Ponto Final,** **Masterkey,** **Base de Dados**e **Recolha** em conformidade.

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

3. Utilize o seguinte código python para carregar os dados e criar uma visão temporária.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Utilize o seguinte comando mágico para executar uma declaração SQL que devolve dados.

    ```python
    %sql
    select * from storm
    ```

    Ligou com sucesso o seu espaço de trabalho de Databricks injetado em VNet a um recurso Cosmos DB ativado por um ponto de serviço. Para ler mais sobre como se conectar ao Cosmos DB, consulte [Azure Cosmos DB Connector para Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o espaço de trabalho azure Databricks e todos os recursos relacionados. Apagar o trabalho evita faturação desnecessária. Se planeia utilizar o espaço de trabalho azure Databricks no futuro, pode parar o cluster e reiniciá-lo mais tarde. Se não vai continuar a utilizar este espaço de trabalho Azure Databricks, elimine todos os recursos que criou neste tutorial utilizando os seguintes passos:

1. A partir do menu à esquerda no portal Azure, clique em **grupos de Recursos** e, em seguida, clique no nome do grupo de recursos que criou.

2. Na página do grupo de recursos, selecione **Eliminar**, digite o nome do recurso para apagar na caixa de texto e, em seguida, selecione **Apagar** novamente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você implementou um espaço de trabalho Azure Databricks para uma rede virtual, e usou o conector Cosmos DB Spark para consultar dados do Cosmos DB a partir de Databricks. Para saber mais sobre trabalhar com os Azure Databricks numa rede virtual, continue a ser tutorial para usar o SQL Server com Os Tijolos de Dados Azure.

> [!div class="nextstepaction"]
> [Tutorial: Consulta um recipiente SQL Server Linux Docker numa rede virtual a partir de um caderno Azure Databricks](vnet-injection-sql-server.md)
