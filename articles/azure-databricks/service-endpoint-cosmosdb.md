---
title: Implementar o Azure Databricks com um ponto de final do Cosmos DB
description: Este tutorial descreve como implementar o Azure Databricks numa rede virtual com um ponto de extremidade de serviço ativado para o Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: d1268ea2cfc22e6350edb32230588a497be8bc79
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054660"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Tutorial: Implementar o Azure Databricks com um ponto de final do Cosmos DB

Este tutorial descreve como implementar uma VNet injetado Databricks ambiente com um ponto de extremidade de serviço ativado para o Cosmos DB.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma área de trabalho do Azure Databricks numa rede virtual
> * Criar um ponto de final de serviço do Cosmos DB
> * Criar uma conta do Cosmos DB e importar dados
> * Criar um cluster do Azure Databricks
> * Consultar o Cosmos DB a partir de um bloco de notas do Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, faça o seguinte:

* Criar uma [área de trabalho do Azure Databricks numa rede virtual](quickstart-create-databricks-workspace-vnet-injection.md).

* Transfira o [conector do Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Transferir dados de exemplo a partir da [NOAA National centros de informações ambientais](https://www.ncdc.noaa.gov/stormevents/). Selecione um Estado ou a área e selecione **pesquisa**. Na página seguinte, aceite as predefinições e selecione **pesquisa**. Em seguida, selecione **transferir CSV** no lado esquerdo da página para transferir os resultados.

* Transfira o [binário compilado previamente](https://aka.ms/csdmtool) da ferramenta de migração de dados do Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Criar um ponto de final de serviço do Cosmos DB

1. Assim que tiver implementado uma área de trabalho do Azure Databricks para uma rede virtual, navegue para a rede virtual no [portal do Azure](https://portal.azure.com). Tenha em atenção as sub-redes privadas e públicas que foram criadas durante a implementação do Databricks.

   ![Sub-redes da rede virtual](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selecione o *público sub-rede* e criar um ponto de final de serviço do Cosmos DB. Em seguida, **guardar**.
   
   ![Adicionar um ponto de extremidade de serviço do Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Criar uma conta do Cosmos DB

1. Abra o portal do Azure. No lado do canto superior esquerdo do ecrã, selecione **criar um recurso > bases de dados > do Azure Cosmos DB**.

2. Preencha o **detalhes da instância** sobre o **Noções básicas** separador com as seguintes definições:

   |Definição|Valor|
   |-------|-----|
   |Subscrição|*a sua subscrição*|
   |Grupo de Recursos|*O grupo de recursos*|
   |Nome da Conta|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |Location|EUA Oeste|
   |Redundância geográfica|Desativar|
   |Escritas de várias regiões|Ativar|

   ![Adicionar um ponto de extremidade de serviço do Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selecione o **rede** separador e configurar a rede virtual. 

   a. Escolha a rede virtual que criou como pré-requisito e, em seguida, selecione *público sub-rede*. Tenha em atenção que *private-sub-rede* tem a nota *ponto final de 'Microsoft AzureCosmosDB' está em falta "* . Isto acontece porque ativada apenas o ponto de extremidade de serviço do Cosmos DB no *público sub-rede*.

   b. Certifique-se de que tenha **permitir o acesso a partir do portal do Azure** ativada. Esta definição permite-lhe acesso a sua conta do Cosmos DB no portal do Azure. Se esta opção estiver definida como **negar**, receberá erros ao tentar aceder à sua conta. 

   > [!NOTE]
   > Não é necessário para este tutorial, mas também é possível habilitar *permitir o acesso a partir do meu IP* se pretender que a capacidade de aceder à sua conta de Cosmos DB a partir do seu computador local. Por exemplo, se estiver a ligar à sua conta com o SDK do Cosmos DB, tem de ativar esta definição. Se estiver desativada, irá receber erros de "Acesso negado".

   ![Definições de rede de conta do DB cosmos](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selecione **rever + criar**e, em seguida **criar** para criar a sua conta do Cosmos DB dentro da rede virtual.

5. Assim que tiver sido criada a sua conta do Cosmos DB, navegue até **chaves** sob **definições**. Copie a cadeia de ligação primária e guarde-o num editor de texto para utilização posterior.

    ![Página de chaves de conta do cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selecione **Data Explorer** e **nova coleção** para adicionar uma nova base de dados e uma coleção para a sua conta do Cosmos DB.

    ![Nova coleção do cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Carregar dados para Cosmos DB

1. Abrir a versão de interface gráfica dos [ferramenta de migração de dados para Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Ferramenta de migração de dados do cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na **informações da origem** separador, selecione **ficheiros CSV** no **importar a partir de** lista pendente. Em seguida, selecione **Add Files** e adicione os dados de storm CSV que transferiu como pré-requisito.

    ![Informações da origem de ferramenta de migração de dados do DB cosmos](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Sobre o **informações do destino** separador, a cadeia de ligação de entrada. O formato de cadeia de ligação é `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. O AccountEndpoint e AccountKey estão incluídos na cadeia de ligação primária que guardou na secção anterior. Acrescentar `Database=<your database name>` no final da cadeia de ligação e selecione **verificar**. Em seguida, adicione a chave de partição e o nome de coleção.

    ![Informações de destino de ferramenta de migração de dados do DB cosmos](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selecione **seguinte** até chegar à página de resumo. Em seguida, selecione **importação**.

## <a name="create-a-cluster-and-add-library"></a>Criar um cluster e adicionar biblioteca

1. Navegue para o serviço do Azure Databricks no [portal do Azure](https://portal.azure.com) e selecione **iniciar área de trabalho**.

   ![Iniciar a área de trabalho do Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Crie um novo cluster. Escolha um nome de Cluster e aceite as predefinições restantes.

   ![Novas definições de cluster](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Depois do cluster é criado, navegue para a página de cluster e selecione o **bibliotecas** separador. Selecione **instalar novo** e carregue o ficheiro de jar de conector do Spark para instalar a biblioteca.

    ![Instalar a biblioteca de conectores do Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Pode verificar que a biblioteca foi instalada no **bibliotecas** separador.

    ![Separador de bibliotecas de cluster do Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Consultar o Cosmos DB a partir de um bloco de notas do Databricks

1. Navegue para a área de trabalho do Azure Databricks e criar um novo bloco de notas do python.

    ![Criar novo bloco de notas do Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Execute o python seguinte código para definir a configuração da ligação do Cosmos DB. Alteração da **ponto final**, **Masterkey**, **base de dados**, e **coleção** em conformidade.

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

3. Utilize o seguinte código de python para carregar os dados e criar uma vista temporária.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Utilize o seguinte comando mágico para executar uma instrução SQL que retorna dados.

    ```python
    %sql
    select * from storm
    ```

    Ligou com êxito a área de trabalho do Databricks injetado de VNet a um recurso do ponto final de serviço ativado do Cosmos DB. Para ler mais sobre como ligar ao Cosmos DB, veja [conector do Azure Cosmos DB para o Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a área de trabalho do Azure Databricks e todos os recursos relacionados. A eliminação da tarefa evita a faturação desnecessária. Se estiver a planear utilizar a área de trabalho do Azure Databricks no futuro, pode parar o cluster e reiniciá-lo mais tarde. Se não pretender continuar a utilizar esta área de trabalho do Azure Databricks, elimine todos os recursos criados neste tutorial, utilizando os seguintes passos:

1. No menu esquerdo no portal do Azure, clique em **grupos de recursos** e, em seguida, clique no nome do grupo de recursos que criou.

2. Na página do grupo de recursos, selecione **elimine**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **eliminar** novamente.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou uma área de trabalho do Azure Databricks para uma rede virtual e, utilizado o conector do Spark do Cosmos DB para consultar dados do Cosmos DB do Databricks. Para saber mais sobre como trabalhar com o Azure Databricks numa rede virtual, avance para o tutorial para utilizar o SQL Server com o Azure Databricks.

> [!div class="nextstepaction"]
> [Tutorial: Consultar um contentor de Docker do SQL Server Linux numa rede virtual a partir de um bloco de notas do Azure Databricks](vnet-injection-sql-server.md)
