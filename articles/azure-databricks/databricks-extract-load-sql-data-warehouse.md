---
title: Tutorial – executar operações de ETL usando Azure Databricks
description: Neste tutorial, saiba como extrair dados de Data Lake Storage Gen2 para Azure Databricks, transformar os dados e, em seguida, carregar os dados no Azure SQL Data Warehouse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 4c73a1e314888d99f4a5beea997265d28077e847
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898614"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutorial: extrair, transformar e carregar dados usando Azure Databricks

Neste tutorial, você executa uma operação ETL (extrair, transformar e carregar dados) usando Azure Databricks. Você extrai dados de Azure Data Lake Storage Gen2 para Azure Databricks, executa transformações nos dados em Azure Databricks e carrega os dados transformados no Azure SQL Data Warehouse.

Os passos neste tutorial utilizam o conector SQL Data Warehouse para o Azure Databricks para transferir dados para este último serviço. Por sua vez, este conector utiliza o Armazenamento de Blobs do Azure como armazenamento temporário para os dados que estão a ser transferidos entre um cluster do Azure Databricks e o Azure SQL Data Warehouse.

A ilustração seguinte mostra o fluxo da aplicação:

![Azure Databricks com Data Lake Store e SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks com Data Lake Store e SQL Data Warehouse")

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Crie um serviço de Azure Databricks.
> * Crie um cluster Spark no Azure Databricks.
> * Crie um sistema de arquivos na conta de Data Lake Storage Gen2.
> * Carregue dados de exemplo para a conta de Azure Data Lake Storage Gen2.
> * Crie uma entidade de serviço.
> * Extrair dados da conta de Azure Data Lake Storage Gen2.
> * Transformar dados em Azure Databricks.
> * Carregar dados no Azure SQL Data Warehouse.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!Note]
> Este tutorial não pode ser executado usando a **assinatura de avaliação gratuita do Azure**.
> Se você tiver uma conta gratuita, vá para seu perfil e altere sua assinatura para pré- **pago**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)e [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para vCPUs em sua região. Ao criar seu espaço de trabalho Azure Databricks, você pode selecionar o tipo de preço de **avaliação (Premium-14 dias gratuitos DBUs)** para dar ao espaço de trabalho o acesso Premium Azure Databricks DBUs por 14 dias.
     
## <a name="prerequisites"></a>Pré-requisitos

Conclua estas tarefas antes de iniciar este tutorial:

* Crie um data warehouse de SQL do Azure, crie uma regra de firewall no nível de servidor e conecte-se ao servidor como um administrador de servidor. Consulte [início rápido: criar e consultar um Azure SQL data warehouse no portal do Azure](../sql-data-warehouse/create-data-warehouse-portal.md).

* Crie uma chave mestra para o data warehouse SQL do Azure. Consulte [criar uma chave mestra de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Crie uma conta de armazenamento de Blobs do Azure e um contentor no mesmo. Obtenha também a chave de acesso para aceder à conta de armazenamento. Consulte [início rápido: carregar, baixar e listar BLOBs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

* Crie uma conta de armazenamento Azure Data Lake Storage Gen2. Consulte [início rápido: criar uma conta de armazenamento Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Crie uma entidade de serviço. Consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Há algumas coisas específicas que você precisará fazer ao executar as etapas nesse artigo.

   * Ao executar as etapas na seção [atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) do artigo, certifique-se de atribuir a função de **colaborador de dados de blob de armazenamento** à entidade de serviço no escopo da conta de data Lake Storage Gen2. Se você atribuir a função ao grupo de recursos ou à assinatura pai, receberá erros relacionados a permissões até que essas atribuições de função se propaguem para a conta de armazenamento.

      Se você preferir usar uma lista de controle de acesso (ACL) para associar a entidade de serviço a um arquivo ou diretório específico, referencie o [controle de acesso em Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Ao executar os passos nos [valores Get para assinar na](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, colhe o ID do inquilino, o ID da aplicação e os valores secretos num ficheiro de texto. Você precisará delas em breve.

* Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Reúna as informações de que você precisa

Certifique-se de concluir os pré-requisitos deste tutorial.

   Antes de começar, você deve ter estes itens de informação:

   :heavy_check_mark: O nome da base de dados, o nome do servidor de base de dados, o nome do utilizador e a palavra-passe do seu armazém de dados Azure SQL.

   :heavy_check_mark: A chave de acesso da sua conta de armazenamento blob.

   :heavy_check_mark: O nome da sua conta de armazenamento gen2 de armazenamento data Lake.

   :heavy_check_mark: A identificação do inquilino da sua assinatura.

   :heavy_check_mark: O ID da aplicação que registou no Azure Ative Directory (Azure AD).

   :heavy_check_mark: A chave de autenticação da aplicação que registou na Azure AD.

## <a name="create-an-azure-databricks-service"></a>Criar um serviço de Azure Databricks

Nesta seção, você cria um serviço de Azure Databricks usando o portal do Azure.

1. No menu portal do Azure, selecione **criar um recurso**.

    ![Criar um recurso em portal do Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Em seguida, selecione **análise** > **Azure Databricks**.

    ![Criar Azure Databricks em portal do Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. Em **Azure Databricks Service**, forneça os seguintes valores para criar um serviço databricks:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks.        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**.  Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).      |
    |**Escalão de Preço**     |  Selecione **padrão**.     |

3. A criação da conta demora alguns minutos. Para monitorar o status da operação, exiba a barra de progresso na parte superior.

4. Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Criar um cluster do Spark no Azure Databricks

1. Na portal do Azure, vá para o serviço databricks que você criou e selecione **Iniciar espaço de trabalho**.

2. Você será redirecionado para o portal de Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar um cluster Spark do databricks no Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Criar um cluster Spark do databricks no Azure")

4. Preencha os valores para os campos seguintes e aceite os valores predefinidos para os outros campos:

    * Introduza um nome para o cluster.

    * Certifique-se de que seleciona o **'Terminar' após \_\_ minutos da caixa de verificação de inatividade.** Se o cluster não estiver sendo usado, forneça uma duração (em minutos) para encerrar o cluster.

    * Selecione **Criar cluster**. Depois que o cluster estiver em execução, você poderá anexar blocos de anotações ao cluster e executar trabalhos do Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Criar um sistema de arquivos na conta de Azure Data Lake Storage Gen2

Nesta seção, você cria um bloco de anotações no espaço de trabalho Azure Databricks e, em seguida, executa trechos de código para configurar a conta de armazenamento

1. Na [portal do Azure](https://portal.azure.com), vá para o serviço de Azure Databricks que você criou e selecione **Iniciar espaço de trabalho**.

2. À esquerda, selecione **espaço de trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um bloco de anotações no databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Criar bloco de anotações no databricks")

3. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Scala** como a linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Fornecer detalhes para um bloco de anotações no databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Fornecer detalhes para um bloco de anotações no databricks")

4. Selecione **Criar**.

5. O bloco de código a seguir define as credenciais de entidade de serviço padrão para qualquer conta de Gen 2 do ADLS acessada na sessão do Spark. O segundo bloco de código acrescenta o nome da conta à configuração para especificar as credenciais para uma conta específica do ADLS Gen 2.  Copie e cole o bloco de código na primeira célula do seu Azure Databricks notebook.

   **Configuração de sessão**

   ```scala
   val appID = "<appID>"
   val password = "<password>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
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

6. Neste bloco de código, substitua os valores `<app-id>`, `<secret>`, `<tenant-id>`e `<storage-account-name>` espaço reservado neste bloco de código supor os valores recolhidos ao completar os pré-requisitos deste tutorial. Substitua o valor de espaço reservado `<file-system-name>` por qualquer nome que você deseja dar ao sistema de arquivos.

   * Os `<app-id>`e `<secret>` são da app que registou com diretório ativo como parte da criação de um diretor de serviço.

   * O `<tenant-id>` é de sua assinatura.

   * O `<storage-account-name>` é o nome da sua conta de armazenamento de Azure Data Lake Storage Gen2.

7. Pressione as teclas **Shift + Enter** para executar o código neste bloco.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Ingerir dados de exemplo na conta de Azure Data Lake Storage Gen2

Antes de começar esta secção, tem de satisfazer os seguintes pré-requisitos:

Introduza o seguinte código numa célula do bloco de notas:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Na célula, pressione **Shift + Enter** para executar o código.

Agora, em uma nova célula abaixo desta, insira o código a seguir e substitua os valores que aparecem entre colchetes com os mesmos valores que você usou anteriormente:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

Na célula, pressione **Shift + Enter** para executar o código.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrair dados da conta de Azure Data Lake Storage Gen2

1. Agora você pode carregar o arquivo JSON de exemplo como um quadro de dados em Azure Databricks. Cole o código a seguir em uma nova célula. Substitua os espaços reservados mostrados entre colchetes pelos valores.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```
2. Pressione as teclas **Shift + Enter** para executar o código neste bloco.

3. Execute o seguinte código para ver o conteúdo do quadro de dados:

    ```scala
    df.show()
    ```
   Verá um resultado semelhante ao seguinte fragmento:

   ```bash
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

Os dados da amostra crua **small_radio_json ficheiro.json** captura o público para uma estação de rádio e tem uma variedade de colunas. Nesta seção, você transforma os dados para recuperar apenas as colunas específicas do conjunto.

1. Primeiro, recupere apenas as colunas **FirstName**, **LastName**, **sexo**, **Location**e **Level** a partir do dataframe que você criou.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Você recebe a saída conforme mostrado no trecho a seguir:

   ```bash
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

   Você recebe a saída conforme mostrado no trecho a seguir.

   ```bash
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

Nesta secção, vai carregar os dados transformados para o Azure SQL Data Warehouse. Você usa o conector de SQL Data Warehouse do Azure para Azure Databricks para carregar diretamente um dataframe como uma tabela em um data warehouse SQL.

Como mencionado anteriormente, o conector de SQL Data Warehouse usa o armazenamento de BLOBs do Azure como armazenamento temporário para carregar dados entre Azure Databricks e o Azure SQL Data Warehouse. Assim, comece por indicar a configuração para ligar à conta de armazenamento. Você já deve ter criado a conta como parte dos pré-requisitos para este artigo.

1. Indique a configuração para aceder à conta de Armazenamento do Azure a partir do Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Especifique uma pasta temporária a ser usada ao mover dados entre Azure Databricks e o SQL Data Warehouse do Azure.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Execute o seguinte fragmento para armazenar as chaves de acesso do armazenamento de Blobs do Azure na configuração. Essa ação garante que você não precise manter a chave de acesso no bloco de anotações em texto sem formatação.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Indique os valores para ligar à instância do Azure SQL Data Warehouse. Você deve ter criado um data warehouse do SQL como um pré-requisito. Use o nome do servidor totalmente qualificado para **dwServer**. Por exemplo, `<servername>.database.windows.net`.

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

5. Execute o trecho a seguir para carregar o dataframe transformado, **renamedColumnsDF**, como uma tabela em um data warehouse do SQL. Este fragmento cria uma tabela chamada **SampleTable** na base de dados SQL.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Esta amostra utiliza a bandeira `forward_spark_azure_storage_credentials`, o que faz com que o SQL Data Warehouse aceda a dados do armazenamento de blob utilizando uma Chave de Acesso. Esse é o único método de autenticação com suporte.
   >
   > Se o armazenamento de BLOBs do Azure estiver restrito a selecionar redes virtuais, SQL Data Warehouse requer [identidade de serviço gerenciada em vez de chaves de acesso](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Isso causará o erro "esta solicitação não está autorizada a executar esta operação".

6. Conecte-se ao banco de dados SQL e verifique se você vê um banco de dados chamado **SampleTable**.

   ![Verificar a tabela de exemplo](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Verificar tabela de exemplo")

7. Execute uma consulta select para verificar os conteúdos da tabela. A tabela deve ter os mesmos dados que o dataframe **renamedColumnsDF** .

    ![Verificar o conteúdo da tabela de exemplo](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Verificar o conteúdo da tabela de exemplo")

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, você pode encerrar o cluster. No espaço de trabalho Azure Databricks, selecione **clusters** à esquerda. Para que o cluster seja encerrado, em **ações**, aponte para as reticências (...) e selecione o ícone **terminar** .

![Parar um cluster do databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Parar um cluster do databricks")

Se não encerrar manualmente o cluster, para automaticamente, desde que selecione o **Terminate após \_\_ minutos de** caixa de verificação de inatividade quando criou o cluster. Nesse caso, o cluster será interrompido automaticamente se estiver inativo durante o tempo especificado.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um serviço de Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar um bloco de notas no Azure Databricks
> * Extrair dados de uma conta de Data Lake Storage Gen2
> * Transformar dados no Azure Databricks
> * Carregar dados para o Azure SQL Data Warehouse

Avance para o próximo tutorial para saber como transmitir dados em tempo real em fluxo para o Azure Databricks mediante a utilização dos Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
>[Transmitir dados para o Azure Databricks utilizando os Hubs de Eventos](databricks-stream-from-eventhubs.md)
