---
title: 'Tutorial: Azure Data Lake Storage Gen2, Azure Databricks & Spark [ Microsoft Docs'
description: Este tutorial mostra como executar consultas spark em um cluster Azure Databricks para aceder a dados em uma conta de armazenamento Azure Data Lake Armazenamento Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: b5f1d0712098e4fe331607860f6e0ed488d29c1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87848797"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Tutorial: Azure Data Lake Storage Gen2, Azure Databricks & Spark

Este tutorial mostra-lhe como ligar o seu cluster Azure Databricks a dados armazenados numa conta de armazenamento Azure que tem o Azure Data Lake Storage Gen2 ativado. Esta ligação permite-lhe executar consultas e análises nativas do seu cluster nos seus dados.

Neste tutorial, irá:

> [!div class="checklist"]
> * Criar um cluster do Databricks
> * Ingerir dados não estruturados numa conta de armazenamento
> * Execute análises nos seus dados no armazenamento blob

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma conta Azure Data Lake Storage Gen2.

  Consulte [criar uma conta Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Certifique-se de que a sua conta de utilizador tem a [função de Contribuinte de Dados de Armazenamento Blob](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) que lhe é atribuída.

* Instale a AzCopy v10. Ver [dados de transferência com AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Criar um diretor de serviço. Ver [Como: Utilizar o portal para criar uma aplicação AD Azure e um responsável de serviço que possa aceder aos recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

  Há algumas coisas específicas que terás de fazer enquanto executas os passos nesse artigo.

  :heavy_check_mark: Ao executar os passos na [atribuição da aplicação a uma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) secção de função do artigo, certifique-se de atribuir a função de Contribuinte de **Dados de Armazenamento** ao titular do serviço.

  > [!IMPORTANT]
  > Certifique-se de atribuir a função no âmbito da conta de armazenamento do Data Lake Gen2. Pode atribuir uma função ao grupo de recursos-mãe ou subscrição, mas receberá erros relacionados com permissões até que essas atribuições de funções se propaguem na conta de armazenamento.

  :heavy_check_mark: Ao executar os passos nos [valores get para a assinatura na](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, cole o ID do inquilino, o ID da aplicação e os valores secretos do cliente num ficheiro de texto. Vai precisar disso em breve.

### <a name="download-the-flight-data"></a>Transferir os dados de voos

Este tutorial utiliza dados de voo do Bureau of Transportation Statistics para demonstrar como realizar uma operação ETL. Tem de descarregar estes dados para completar o tutorial.

1. Vá à [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Selecione a caixa de verificação **prezipped para** selecionar todos os campos de dados.

3. Selecione o botão **Descarregar** e guarde os resultados para o seu computador. 

4. Desaperte o conteúdo do ficheiro com fecho e tome nota do nome do ficheiro e do caminho do ficheiro. Precisa desta informação mais tarde.

## <a name="create-an-azure-databricks-service"></a>Criar um serviço Azure Databricks

Nesta secção, cria-se um serviço Azure Databricks utilizando o portal Azure.

1. No portal Azure, **selecione Criar um recurso**  >  **Analytics**  >  **Azure Databricks**.

    ![Databricks no portal Azure](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks no portal Azure")

2. No serviço **Azure Databricks,** forneça os seguintes valores para criar um serviço Databricks:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks.  |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).       |
    |**Escalão de Preço**     |  Selecione **Standard**.     |

    ![Criar uma área de trabalho do Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Criar um serviço Azure Databricks")

3. A criação da conta demora alguns minutos. Para monitorizar o estado de funcionamento, veja a barra de progresso no topo.

4. Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Criar um cluster do Spark no Azure Databricks

1. No portal Azure, aceda ao serviço Databricks que criou e selecione **Launch Workspace**.

2. Está redirecionado para o portal Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks em Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks em Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar conjunto de faíscas de dados no Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Criar conjunto de faíscas de dados no Azure")

    Preencha os valores para os campos seguintes e aceite os valores predefinidos para os outros campos:

    - Introduza um nome para o cluster.
     
    - Certifique-se de que seleciona a caixa de verificação **Terminar após 120 minutos de inatividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

4. Selecione **Criar cluster**. Depois do cluster estar em funcionamento, pode anexar cadernos ao cluster e executar trabalhos de Faísca.

## <a name="ingest-data"></a>Ingerir dados

### <a name="copy-source-data-into-the-storage-account"></a>Copiar dados de origem para a conta de armazenamento

Utilize o AzCopy para copiar dados do seu ficheiro *.csv* na sua conta Desaquise de Armazenamento de Dados Gen2.

1. Abra uma janela de pedido de comando e insira o seguinte comando para iniciar sessão na sua conta de armazenamento.

   ```bash
   azcopy login
   ```

   Siga as instruções que aparecem na janela de pedido de comando para autenticar a sua conta de utilizador.

2. Para copiar dados da conta *.csv,* insira o seguinte comando.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Substitua o `<csv-folder-path>` valor do espaço reservado pelo caminho para o ficheiro *.csv.*

   * Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

   * Substitua o `<container-name>` espaço reservado pelo nome de um recipiente na sua conta de armazenamento.

## <a name="create-a-container-and-mount-it"></a>Crie um recipiente e monte-o

Nesta secção, irá criar um recipiente e uma pasta na sua conta de armazenamento.

1. No [portal Azure,](https://portal.azure.com)vá ao serviço Azure Databricks que criou e selecione **Launch Workspace**.

2. À esquerda, selecione **Workspace**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um caderno em Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Criar caderno em Databricks")

3. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Python** como o idioma e, em seguida, selecione o cluster Spark que criou anteriormente.

4. Selecione **Criar**.

5. Copie e cole o seguinte bloco de código na primeira célula, mas não execute este código ainda.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. Neste bloco de códigos, substitua os valores , e espaços neste bloco de `appId` `clientSecret` `tenant` `storage-account-name` códigos pelos valores que recolheu ao completar os pré-requisitos deste tutorial. Substitua o `container-name` valor do espaço reservado pelo nome do recipiente.

19. Prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

   Mantenha este caderno aberto, pois irá adicionar-lhe comandos mais tarde.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Utilize o Databricks Notebook para converter CSV em Parquet

No caderno que criou anteriormente, adicione uma nova célula e cole o seguinte código nessa célula. 

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

Numa nova célula, cole o seguinte código para obter uma lista de ficheiros CSV enviados via AzCopy.

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

Para criar quadros de dados para as suas fontes de dados, execute o seguinte script:

* Substitua o `<csv-folder-path>` valor do espaço reservado pelo caminho para o ficheiro *.csv.*

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

Introduza este script para executar algumas consultas básicas de análise contra os dados.

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

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, apague o grupo de recursos e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a conta de armazenamento e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Extrair, transformar e carregar dados com o Apache Hive no Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
