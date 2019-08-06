---
title: 'Tutorial: Acessar dados do Azure Data Lake Storage Gen2 com Azure Databricks usando o Spark | Microsoft Docs'
description: Este tutorial mostra como executar consultas do Spark em um cluster Azure Databricks para acessar dados em uma conta de armazenamento de Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: a745ade33e5d9c493fed187bbd9a4309e1a2d0ff
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360113"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Tutorial: Acessar dados do Data Lake Storage Gen2 com Azure Databricks usando o Spark

Este tutorial mostra como conectar o cluster Azure Databricks aos dados armazenados em uma conta de armazenamento do Azure que tenha Azure Data Lake Storage Gen2 habilitado. Essa conexão permite que você execute de forma nativa consultas e análises do seu cluster em seus dados.

Neste tutorial, irá:

> [!div class="checklist"]
> * Criar um cluster do Databricks
> * Ingerir dados não estruturados numa conta de armazenamento
> * Execute a análise em seus dados no armazenamento de BLOBs

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma conta de Azure Data Lake Storage Gen2.

  Consulte [criar uma conta de Azure data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Certifique-se de que sua conta de usuário tenha a [função de colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) atribuída a ela.

* Instale o AzCopy v10. Consulte [transferir dados com AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Crie uma entidade de serviço. Consulte [como: Use o portal para criar um aplicativo do Azure AD e uma entidade de serviço que](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)possa acessar recursos.

  Há algumas coisas específicas que você precisará fazer ao executar as etapas nesse artigo.

  :heavy_check_mark: Ao executar as etapas na seção [atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) do artigo, certifique-se de atribuir a função de **colaborador de dados de blob de armazenamento** à entidade de serviço.

  > [!IMPORTANT]
  > Certifique-se de atribuir a função no escopo da conta de armazenamento Data Lake Storage Gen2. Você pode atribuir uma função ao grupo de recursos ou à assinatura pai, mas receberá erros relacionados a permissões até que essas atribuições de função se propaguem para a conta de armazenamento.

  :heavy_check_mark: Ao executar as etapas na seção [obter valores para entrar no](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artigo, Cole a ID do locatário, a ID do aplicativo e os valores de senha em um arquivo de texto. Você precisará delas em breve.

### <a name="download-the-flight-data"></a>Transferir os dados de voos

Este tutorial usa dados de vôo do Bureau de estatísticas de transporte para demonstrar como executar uma operação de ETL. Você deve baixar esses dados para concluir o tutorial.

1. Vá para [pesquisa e administração inovadora de tecnologia, Bureau de estatísticas de transporte](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Marque a caixa de seleção **arquivo prezipado** para selecionar todos os campos de dados.

3. Selecione o botão **baixar** e salve os resultados em seu computador. 

4. Descompacte o conteúdo do arquivo compactado e anote o nome do arquivo e o caminho do arquivo. Você precisará dessas informações em uma etapa posterior.

## <a name="create-an-azure-databricks-service"></a>Criar um serviço de Azure Databricks

Nesta seção, você cria um serviço de Azure Databricks usando o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Azure Databricks Service**, forneça os seguintes valores para criar um serviço databricks:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks.  |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).       |
    |**Escalão de Preço**     |  Selecione **padrão**.     |

    ![Criar um espaço de trabalho Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Criar um serviço de Azure Databricks")

3. A criação da conta demora alguns minutos. Para monitorar o status da operação, exiba a barra de progresso na parte superior.

4. Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Criar um cluster do Spark no Azure Databricks

1. Na portal do Azure, vá para o serviço databricks que você criou e selecione **Iniciar espaço de trabalho**.

2. Você será redirecionado para o portal de Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar um cluster Databricks Spark no Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Criar um cluster Databricks Spark no Azure")

4. Preencha os valores para os campos seguintes e aceite os valores predefinidos para os outros campos:

    * Introduza um nome para o cluster.

    * Para este artigo, crie um cluster com o tempo de execução **5,1** .

    * Certifique-se de marcar a caixa de seleção **terminar após \_ \_ minutos de inatividade** . Se o cluster não estiver sendo usado, forneça uma duração (em minutos) para encerrar o cluster.

    * Selecione **Criar cluster**. Depois que o cluster estiver em execução, você poderá anexar blocos de anotações ao cluster e executar trabalhos do Spark.

## <a name="ingest-data"></a>Ingerir dados

### <a name="copy-source-data-into-the-storage-account"></a>Copiar dados de origem para a conta de armazenamento

Use AzCopy para copiar dados do seu arquivo *. csv* para sua conta do data Lake Storage Gen2.

1. Abra uma janela de prompt de comando e digite o seguinte comando para fazer logon em sua conta de armazenamento.

   ```bash
   azcopy login
   ```

   Siga as instruções que aparecem na janela do prompt de comando para autenticar sua conta de usuário.

2. Para copiar dados da conta *. csv* , insira o comando a seguir.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time.csv
   ```

   * Substitua o `<csv-folder-path>` valor do espaço reservado pelo caminho para o arquivo *. csv* .

   * Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

   * Substitua o `<file-system-name>` espaço reservado por qualquer nome que você deseja fornecer ao sistema de arquivos.

## <a name="create-a-file-system-and-mount-it"></a>Criar um sistema de arquivos e montá-lo

Nesta seção, você criará um sistema de arquivos e uma pasta em sua conta de armazenamento.

1. Na [portal do Azure](https://portal.azure.com), vá para o serviço de Azure Databricks que você criou e selecione **Iniciar espaço de trabalho**.

2. À esquerda, selecione **espaço de trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um bloco de anotações no databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Criar bloco de anotações no") databricks

3. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Python** como o idioma e, em seguida, selecione o cluster Spark que você criou anteriormente.

4. Selecione **Criar**.

5. Copie e cole o bloco de código a seguir na primeira célula, mas não execute esse código ainda.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. Nesse bloco de código, substitua os `appId`valores `password`de `tenant`espaço reservado `storage-account-name` ,, e nesse bloco de código pelos valores que você coletou ao concluir os pré-requisitos deste tutorial. Substitua o `file-system-name` valor do espaço reservado pelo nome que você atribuiu ao sistema de arquivos ADLS na etapa anterior.

Use esses valores para substituir os espaços reservados mencionados.

   * O `appId`, e `password` são do aplicativo que você registrou com o Active Directory como parte da criação de uma entidade de serviço.

   * O `tenant-id` é de sua assinatura.

   * O `storage-account-name` é o nome da sua conta de armazenamento Azure data Lake Storage Gen2.

   * Substitua o `file-system-name` espaço reservado por qualquer nome que você deseja fornecer ao sistema de arquivos.

   > [!NOTE]
   > Em uma configuração de produção, considere armazenar sua senha no Azure Databricks. Em seguida, adicione uma chave de pesquisa ao bloco de código em vez da senha. Depois de concluir este guia de início rápido, consulte o artigo [Azure data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) no site do Azure Databricks para ver exemplos dessa abordagem.

19. Pressione as teclas **Shift + Enter** para executar o código neste bloco.

   Mantenha este bloco de anotações aberto, pois você adicionará comandos a ele mais tarde.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Utilize o Databricks Notebook para converter CSV em Parquet

No bloco de anotações que você criou anteriormente, adicione uma nova célula e cole o código a seguir nessa célula. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Explorar dados

Em uma nova célula, Cole o código a seguir para obter uma lista de arquivos CSV carregados por meio de AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Para criar um novo ficheiro e listar os ficheiros na pasta *parquet/flights*, execute este script:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Com estes exemplos de código, explorou a natureza hierárquica do HDFS através dos dados armazenados numa conta de armazenamento com o Data Lake Storage Gen2 ativado.

## <a name="query-the-data"></a>Consultar os dados

Em seguida, pode começar a consultar os dados que carregou para a sua conta de armazenamento. Introduza cada um dos seguintes blocos de código em **Cmd 1** e prima **Cmd + Enter** para executar o script de Python.

Para criar quadros de dados para suas fontes de dados, execute o seguinte script:

* Substitua o `<csv-folder-path>` valor do espaço reservado pelo caminho para o arquivo *. csv* .

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Insira esse script para executar algumas consultas básicas de análise nos dados.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua o grupo de recursos e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a conta de armazenamento e selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Extrair, transformar e carregar dados com o Apache Hive no Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
