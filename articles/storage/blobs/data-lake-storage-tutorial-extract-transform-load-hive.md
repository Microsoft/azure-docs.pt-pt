---
title: 'Tutorial: Extract, transform, and load data by using Azure HDInsight'
description: In this tutorial, you learn how to extract data from a raw CSV dataset, transform it by using Apache Hive on Azure HDInsight, and then load the transformed data into Azure SQL Database by using Sqoop.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: c9ed675dc970b093f6407d15b3db2ac2668c626b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327566"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>Tutorial: Extract, transform, and load data by using Azure HDInsight

In this tutorial, you perform an ETL operation: extract, transform, and load data. You take a raw CSV data file, import it into an Azure HDInsight cluster, transform it with Apache Hive, and load it into an Azure SQL database with Apache Sqoop.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Extract and upload the data to an HDInsight cluster.
> * Transform the data by using Apache Hive.
> * Load the data to an Azure SQL database by using Sqoop.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **An Azure Data Lake Storage Gen2 storage account that is configured for HDInsight**

    See [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **A Linux-based Hadoop cluster on HDInsight**

    See [Quickstart: Get started with Apache Hadoop and Apache Hive in Azure HDInsight using the Azure portal](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Azure SQL Database**: You use an Azure SQL database as a destination data store. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure no portal do Azure](../../sql-database/sql-database-get-started.md).

* **Azure CLI**: If you haven't installed the Azure CLI, see [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **A Secure Shell (SSH) client**: For more information, see [Connect to HDInsight (Hadoop) by using SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Browse to [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na página, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar Ano |2013 |
   | Filtrar Período |Janeiro |
   | Campos |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Limpe todos os outros campos.

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="extract-and-upload-the-data"></a>Extract and upload the data

In this section, you'll upload data to your HDInsight cluster and then copy that data to your Data Lake Storage Gen2 account.

1. Open a command prompt and use the following Secure Copy (Scp) command to upload the .zip file to the HDInsight cluster head node:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Replace the `<file-name>` placeholder with the name of the .zip file.
   * Replace the `<ssh-user-name>` placeholder with the SSH login for the HDInsight cluster.
   * Replace the `<cluster-name>` placeholder with the name of the HDInsight cluster.

   Se utilizar uma palavra-passe para autenticar o início de sessão SSH, é-lhe pedida a palavra-passe.

   Se utilizar uma chave pública, poderá ter de utilizar o parâmetro `-i` e especificar o caminho para a chave privada correspondente. Por exemplo, `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Depois de concluído o carregamento, utilize SSH para ligar ao cluster. Na linha de comandos, introduza o seguinte comando:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Utilize o seguinte comando para descomprimir o ficheiro .zip:

   ```bash
   unzip <file-name>.zip
   ```

   The command extracts a **.csv** file.

4. Use the following command to create the Data Lake Storage Gen2 container.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Replace the `<container-name>` placeholder with the name that you want to give your container.

   Replace the `<storage-account-name>` placeholder with the name of your storage account.

5. Use the following command to create a directory.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Use the following command to copy the *.csv* file to the directory:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Use quotes around the file name if the file name contains spaces or special characters.

## <a name="transform-the-data"></a>Transform the data

In this section, you use Beeline to run an Apache Hive job.

As part of the Apache Hive job, you import the data from the .csv file into an Apache Hive table named **delays**.

1. From the SSH prompt that you already have for the HDInsight cluster, use the following command to create and edit a new file named     **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. Modify the following text by replace the `<container-name>` and `<storage-account-name>` placeholders with your container and storage account name. Then copy and paste the text into the nano console by using pressing the SHIFT key along with the right-mouse click button.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Save the file by using use CTRL+X and then type `Y` when prompted.

4. Para iniciar o Hive e executar o ficheiro **flightdelays.hql**, utilize o seguinte comando:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Quando a execução do script __flightdelays.hql__ for concluída, utilize o seguinte comando para abrir uma sessão interativa do Beeline:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. Quando receber a linha de comandos `jdbc:hive2://localhost:10001/>`, utilize a seguinte consulta para obter dados a partir dos dados importados de atrasos de voos:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   Esta consulta obtém uma lista de cidades em que os voos se atrasaram devido às condições atmosféricas, juntamente com o tempo de atraso médio e guarda-a em `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Depois, o Sqoop lê os dados a partir dessa localização e exporta-os para a Base de Dados SQL do Azure.

7. Para sair do Beeline, introduza `!quit` na linha de comandos.

## <a name="create-a-sql-database-table"></a>Criar uma base de dados SQL

You need the server name from your SQL database for this operation. Complete these steps to find your server name.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Select **SQL Databases**.

3. Filter on the name of the database that you choose to use. O nome do servidor é mostrado na coluna **Nome do servidor**.

4. Filter on the name of the database that you want to use. O nome do servidor é mostrado na coluna **Nome do servidor**.

    ![Get Azure SQL server details](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Get Azure SQL server details")

    Existem muitas formas de ligar à Base de Dados SQL e criar uma tabela. Os passos seguintes utilizam [FreeTDS](https://www.freetds.org/) do cluster do HDInsight.

5. Para instalar o FreeTDS, utilize o seguinte comando a partir de uma ligação SSH ao cluster:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. After the installation completes, use the following command to connect to the SQL Database server.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Replace the `<server-name>` placeholder with the SQL Database server name.

   * Replace the `<admin-login>` placeholder with the admin login for SQL Database.

   * Replace the `<database-name>` placeholder with the database name

   When you're prompted, enter the password for the SQL Database admin login.

   Receberá um resultado semelhante ao seguinte texto:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. At the `1>` prompt, enter the following statements:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas.

   The query creates a table named **delays**, which has a clustered index.

9. Use the following query to verify that the table is created:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   O resultado é semelhante ao seguinte texto:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Introduza `exit` na linha de comandos `1>` para sair do utilitário tsql.

## <a name="export-and-load-the-data"></a>Export and load the data

In the previous sections, you copied the transformed data at the location  `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. In this section, you use Sqoop to export the data from `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` to the table you created in the Azure SQL database.

1. Utilize o seguinte comando para verificar se o Sqoop pode ver a sua base de dados SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   The command returns a list of databases, including the database in which you created the **delays** table.

2. Use the following command to export data from the **hivesampletable** table to the **delays** table:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop connects to the database that contains the **delays** table, and exports data from the `/tutorials/flightdelays/output` directory to the **delays** table.

3. After the `sqoop` command finishes, use the tsql utility to connect to the database:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Use the following statements to verify that the data was exported to the **delays** table:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Deverá ver uma lista dos dados na tabela. A tabela inclui o nome da cidade e o tempo médio dos atrasos dos voos.

5. Enter `exit` to exit the tsql utility.

## <a name="clean-up-resources"></a>Limpar recursos

All resources used in this tutorial are preexisting. No cleanup is necessary.

## <a name="next-steps"></a>Passos seguintes

To learn more ways to work with data in HDInsight, see the following article:

> [!div class="nextstepaction"]
> [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
