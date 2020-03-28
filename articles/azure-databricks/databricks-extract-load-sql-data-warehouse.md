---
title: Tutorial - Realizar operações ETL utilizando tijolos de dados Azure
description: Neste tutorial, aprenda a extrair dados do Data Lake Storage Gen2 em Tijolos de Dados Azure, transforme os dados e, em seguida, carregue os dados no Azure SQL Data Warehouse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 8819b79a105b7a654a34e47c5ba9b3d351a1d926
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239418"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutorial: Extrair, transformar e carregar dados utilizando os Databricks Azure

Neste tutorial, realiza uma operação ETL (extrair, transformar e carregar dados) utilizando os Bricks Azure. Extrai dados do Azure Data Lake Storage Gen2 em Azure Databricks, executa transformações nos dados em Tijolos de Dados Azure e carrega os dados transformados em Azure SQL Data Warehouse.

Os passos neste tutorial utilizam o conector SQL Data Warehouse para o Azure Databricks para transferir dados para este último serviço. Por sua vez, este conector utiliza o Armazenamento de Blobs do Azure como armazenamento temporário para os dados que estão a ser transferidos entre um cluster do Azure Databricks e o Azure SQL Data Warehouse.

A ilustração seguinte mostra o fluxo da aplicação:

![Tijolos de dados azure com Data Lake Store e Armazém de Dados SQL](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Tijolos de dados azure com Data Lake Store e Armazém de Dados SQL")

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Crie um serviço Azure Databricks.
> * Crie um cluster de faíscas em Tijolos de Dados Azure.
> * Crie um sistema de ficheiros na conta Data Lake Storage Gen2.
> * Faça upload dos dados da amostra para a conta De armazenamento de lagos Azure Data Gen2.
> * Crie um diretor de serviço.
> * Extrair dados da conta De armazenamento de lagos Azure Data Gen2.
> * Transforme dados em Tijolos de Dados Azure.
> * Carregue os dados no Armazém de Dados Azure SQL.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!Note]
> Este tutorial não pode ser realizado utilizando **a assinatura de teste gratuito do Azure.**
> Se tiver uma conta gratuita, vá ao seu perfil e altere a sua subscrição para **pay-as-you-go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), e [solicite um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota para vCPUs na sua região. Ao criar o seu espaço de trabalho Azure Databricks, pode selecionar o nível de preços **Trial (Premium - 14 dias grátis)** para dar ao espaço de trabalho acesso a DBUs de dados Premium Azure gratuitos durante 14 dias.
     
## <a name="prerequisites"></a>Pré-requisitos

Complete estas tarefas antes de iniciar este tutorial:

* Crie um armazém de dados Azure SQL, crie uma regra de firewall ao nível do servidor e ligue-se ao servidor como administrador do servidor. Ver [Quickstart: Criar e consultar um armazém de dados Azure SQL no portal Azure](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* Crie uma chave principal para o armazém de dados Azure SQL. Ver [Criar uma chave de base de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Crie uma conta de armazenamento de Blobs do Azure e um contentor no mesmo. Obtenha também a chave de acesso para aceder à conta de armazenamento. Ver [Quickstart: Upload, download e lista de bolhas com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

* Crie uma conta de armazenamento de Gen2 de armazenamento de lago de dados Azure. Ver Quickstart: Criar uma conta de armazenamento de armazenamento [de lago de dados Azure Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Crie um diretor de serviço. Ver Como: Utilize o portal para criar uma aplicação e um diretor de serviço da [Azure AD que possam aceder a recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

   Há algumas coisas específicas que terás de fazer enquanto fazes os passos nesse artigo.

   * Ao executar os passos na [Atribuição da aplicação a uma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) secção de papel do artigo, certifique-se de atribuir a função de Colaborador de Dados **blob** de armazenamento ao principal de serviço no âmbito da conta Data Lake Storage Gen2. Se atribuir o papel ao grupo de recursos-mãe ou subscrição, receberá erros relacionados com permissões até que essas atribuições de funções se propaguem na conta de armazenamento.

      Se preferir utilizar uma lista de controlo de acesso (ACL) para associar o diretor de serviço a um ficheiro ou diretório específico, referência Controlo de [acesso em Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Ao executar os passos nos [valores Get para assinar na](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, colhe o ID do inquilino, o ID da aplicação e os valores secretos num ficheiro de texto. Vai precisar disso em breve.

* Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Reúna a informação de que precisa

Certifique-se de que completa os pré-requisitos deste tutorial.

   Antes de começar, deve ter estes itens de informação:

   :heavy_check_mark: O nome da base de dados, o nome do servidor de base de dados, o nome do utilizador e a palavra-passe do seu armazém de dados Azure SQL.

   :heavy_check_mark: A chave de acesso da sua conta de armazenamento blob.

   :heavy_check_mark: O nome da sua conta de armazenamento gen2 de armazenamento data Lake.

   :heavy_check_mark: A identificação do inquilino da sua assinatura.

   :heavy_check_mark: O ID da aplicação que registou no Azure Ative Directory (Azure AD).

   :heavy_check_mark: A chave de autenticação da aplicação que registou na Azure AD.

## <a name="create-an-azure-databricks-service"></a>Criar um serviço Azure Databricks

Nesta secção, cria-se um serviço Azure Databricks utilizando o portal Azure.

1. A partir do menu do portal Azure, selecione **Criar um recurso**.

    ![Criar um recurso no portal Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Em seguida, selecione **Analytics** > **Azure Databricks**.

    ![Criar tijolos de dados Azure no portal Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. No âmbito do Serviço De Tijolos de Dados Azure, forneça os **seguintes valores**para criar um serviço databricks:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks.        |
    |**Assinatura**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**.  Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).      |
    |**Nível de Preços**     |  Selecione **Standard**.     |

3. A criação da conta demora alguns minutos. Para monitorizar o estado de funcionamento, veja a barra de progresso no topo.

4. Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Criar um cluster do Spark no Azure Databricks

1. No portal Azure, vá ao serviço Databricks que criou e selecione **Launch Workspace**.

2. Foi redirecionado para o portal Azure Databricks. No portal, selecione **Cluster**.

    ![Tijolos de dados em Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Tijolos de dados em Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar cluster de faíscas databricks em Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Criar cluster de faíscas databricks em Azure")

4. Preencha os valores para os campos seguintes e aceite os valores predefinidos para os outros campos:

    * Introduza um nome para o cluster.

    * Certifique-se de que seleciona o **'Terminar' após \_ \_ minutos de** caixa de verificação de inatividade. Se o cluster não estiver a ser utilizado, forneça uma duração (em minutos) para terminar o cluster.

    * Selecione **Criar cluster**. Depois do cluster estar em funcionamento, pode anexar cadernos ao cluster e executar trabalhos spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Criar um sistema de ficheiros na conta Azure Data Lake Storage Gen2

Nesta secção, cria-se um bloco de notas no espaço de trabalho do Azure Databricks e, em seguida, executa códigos para configurar a conta de armazenamento

1. No [portal Azure,](https://portal.azure.com)vá ao serviço Azure Databricks que criou e selecione **Launch Workspace**.

2. À esquerda, selecione **Workspace**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um caderno em Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Criar caderno em Databricks")

3. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Scala** como a linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Forneça detalhes para um caderno em Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Forneça detalhes para um caderno em Databricks")

4. Selecione **Criar**.

5. O bloco de código seguinte define as credenciais principais do serviço padrão para qualquer conta ADLS Gen 2 acedida na sessão Spark. O segundo bloco de código anexa o nome da conta à definição para especificar credenciais para uma conta ADLS Gen 2 específica.  Copie e cole qualquer bloco de código na primeira célula do seu caderno Azure Databricks.

   **Configuração da sessão**

   ```scala
   val appID = "<appID>"
   val secret = "<secret>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<secret>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Account configuration** (Configuração da conta)

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val secret = "<secret>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + secret + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. Neste bloco de código, `<secret>` `<tenant-id>`substitua `<storage-account-name>` os `<app-id>`valores de , , e espaço reservado neste bloco de código supor os valores que recolheu ao completar os pré-requisitos deste tutorial. Substitua `<file-system-name>` o valor do espaço reservado por qualquer nome que queira dar ao sistema de ficheiros.

   * E `<app-id>` `<secret>` são da app que registou com diretório ativo como parte da criação de um diretor de serviço.

   * O `<tenant-id>` é da sua assinatura.

   * É `<storage-account-name>` o nome da sua conta de armazenamento do Lago Azure Data Gen2.

7. Prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Ingerir dados de amostrana conta Azure Data Lake Storage Gen2

Antes de começar esta secção, tem de satisfazer os seguintes pré-requisitos:

Introduza o seguinte código numa célula do bloco de notas:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Na célula, prima **SHIFT + ENTER** para executar o código.

Agora, numa nova célula abaixo desta, introduza o seguinte código e substitua os valores que aparecem nos suportes com os mesmos valores que utilizou anteriormente:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

Na célula, prima **SHIFT + ENTER** para executar o código.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrair dados da conta De armazenamento de lagos Azure Data Gen2

1. Agora pode carregar o ficheiro json da amostra como uma moldura de dados em Tijolos de Dados Azure. Cola o seguinte código numa nova célula. Substitua os espaços reservados mostrados nos suportes com os seus valores.

   ```scala
   val df = spark.read.json("abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/small_radio_json.json")
   ```
2. Prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

3. Executar o seguinte código para ver o conteúdo do quadro de dados:

    ```scala
    df.show()
    ```
   Verá um resultado semelhante ao seguinte fragmento:

   ```output
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Os dados foram agora extraídos do Armazenamento do Azure Data Lake Ger2 para o Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Transformar dados no Azure Databricks

Os dados da amostra crua **small_radio_json ficheiro.json** captura o público para uma estação de rádio e tem uma variedade de colunas. Nesta secção, transforma os dados apenas para recuperar colunas específicas do conjunto de dados.

1. Primeiro, recupere apenas as colunas **primeiroNome**, **apelido,** **sexo,** **localização**e **nivelhe a** partir do quadro de dados que criou.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Recebe a saída como mostrado no seguinte corte:

   ```output
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. Pode transformar mais os dados para mudar o nome da coluna **level** para **subscription_type**.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   Recebe a saída como mostrado no seguinte corte.

   ```output
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados para o Azure SQL Data Warehouse

Nesta secção, vai carregar os dados transformados para o Azure SQL Data Warehouse. Utiliza o conector Azure SQL Data Warehouse para os Bricks Azure para fazer o upload direto de um dataframe como tabela num armazém de dados SQL.

Como mencionado anteriormente, o conector SQL Data Warehouse utiliza o armazenamento Azure Blob como armazenamento temporário para fazer upload de dados entre os Databricks Azure e o Azure SQL Data Warehouse. Assim, comece por indicar a configuração para ligar à conta de armazenamento. Já deve ter criado a conta como parte dos pré-requisitos para este artigo.

1. Indique a configuração para aceder à conta de Armazenamento do Azure a partir do Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Especifique uma pasta temporária para utilizar ao mover dados entre os Databricks Azure e o Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Execute o seguinte fragmento para armazenar as chaves de acesso do armazenamento de Blobs do Azure na configuração. Esta ação garante que não é preciso manter a chave de acesso no caderno em texto simples.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Indique os valores para ligar à instância do Azure SQL Data Warehouse. Deve ter criado um armazém de dados SQL como pré-requisito. Utilize o nome do servidor totalmente qualificado para **dwServer**. Por exemplo, `<servername>.database.windows.net`.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Executar o seguinte corte para carregar o quadro de dados transformado, **renomeadoColumnsDF,** como uma tabela num armazém de dados SQL. Este fragmento cria uma tabela chamada **SampleTable** na base de dados SQL.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Esta amostra `forward_spark_azure_storage_credentials` utiliza a bandeira, o que faz com que o SQL Data Warehouse aceda a dados do armazenamento de blob utilizando uma Chave de Acesso. Este é o único método de autenticação suportado.
   >
   > Se o seu Armazenamento De Blob Azure estiver restrito para selecionar redes virtuais, o SQL Data Warehouse requer identidade de [serviço gerida em vez de Chaves de Acesso](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Isto causará o erro "Este pedido não está autorizado a executar esta operação."

6. Ligue-se à base de dados SQL e verifique se vê uma base de dados chamada **SampleTable**.

   ![Verifique a tabela de amostras](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Verificar tabela de amostras")

7. Execute uma consulta select para verificar os conteúdos da tabela. A tabela deve ter os mesmos dados que o quadro de dados **renomeadoColumnsDF.**

    ![Verifique o conteúdo da tabela de amostras](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Verifique o conteúdo da tabela de amostras")

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar o tutorial, pode terminar o agrupamento. A partir do espaço de trabalho Azure Databricks, selecione **Clusters** à esquerda. Para que o cluster termine, ao abrigo de **Ações,** aponte para a elipse (...) e selecione o ícone **'Terminar'.**

![Parar um cluster de Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Parar um cluster de Databricks")

Se não encerrar manualmente o cluster, para automaticamente, desde que selecione o **'Terminate' \_ \_ após minutos de caixa de verificação de inatividade** quando criou o cluster. Neste caso, o cluster para automaticamente se estiver inativo durante o tempo especificado.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um serviço Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar um bloco de notas no Azure Databricks
> * Extrair dados de uma conta Gen2 de Armazenamento de Data Lake
> * Transformar dados no Azure Databricks
> * Carregar dados para o Azure SQL Data Warehouse

Avance para o próximo tutorial para saber como transmitir dados em tempo real em fluxo para o Azure Databricks mediante a utilização dos Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
>[Transmitir dados para o Azure Databricks utilizando os Hubs de Eventos](databricks-stream-from-eventhubs.md)
