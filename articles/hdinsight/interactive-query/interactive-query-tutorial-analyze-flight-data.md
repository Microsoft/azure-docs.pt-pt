---
title: 'Tutorial: Executar operações de ETL usando a consulta interativa no Azure HDInsight'
description: Tutorial-saiba como extrair dados de um conjunto de um CSV bruto, transformá-lo usando a consulta interativa no HDInsight e, em seguida, carregar os dados transformados no banco de dado SQL do Azure usando o Apache Sqoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 07/02/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: 9ff215bb687ea2b6aa32ecb01dba7a61385b15a4
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735837"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando a consulta interativa no Azure HDInsight

Neste tutorial, você pega um arquivo de dados CSV bruto de dados de vôo publicamente disponíveis, importa-o para o armazenamento de cluster do HDInsight e, em seguida, transforma os dados usando a consulta interativa no Azure HDInsight. Depois que os dados são transformados, você os carrega em um banco de dados SQL do Azure usando o [Apache Sqoop](https://sqoop.apache.org/).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Transferir os dados de voos de exemplo
> * Carregar os dados para um cluster do HDInsight
> * Transformar os dados usando a consulta interativa
> * Criar uma tabela em um banco de dados SQL do Azure
> * Usar o Sqoop para exportar dados para um banco de dado SQL do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de consulta interativa no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **consulta interativa** para **tipo de cluster**.

* Um banco de dados SQL do Azure. Vai utilizar uma base de dados SQL do Azure como arquivo de dados de destino. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure no portal do Azure](/azure/sql-database/sql-database-single-database-get-started).

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Navegue até [pesquisa e administração inovadora de tecnologia, Bureau de estatísticas de transporte](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na página, desmarque todos os campos e, em seguida, selecione os seguintes valores:

   | Name | Valor |
   | --- | --- |
   | Filtrar Ano |2019 |
   | Filtrar Período |Janeiro |
   | Campos |Year, voo, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, originate, OriginCityName, Originfile, DestAirportID, dest, DestCityName, Deststate, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Carregar os dados para um cluster do HDInsight

Existem muitas formas de carregar dados para o armazenamento associado a um cluster do HDInsight. Nesta secção, vai utilizar `scp` para carregar os dados. Para ver outras formas de carregar dados, veja [Upload data to HDInsight](../hdinsight-upload-data.md) (Carregar dados para o HDInsight).

1. Carregue o arquivo. zip no nó principal do cluster HDInsight. Edite o comando a seguir `FILENAME` substituindo pelo nome do arquivo. zip e `CLUSTERNAME` pelo nome do cluster HDInsight. Em seguida, abra um prompt de comando, defina seu diretório de trabalho para o local do arquivo e, em seguida, digite o comando.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Se for solicitado que você insira sim ou não para continuar, digite Sim no prompt de comando e pressione Enter. O texto não é visível na janela conforme você digita.

2. Depois de concluído o carregamento, utilize SSH para ligar ao cluster. Edite o comando a seguir `CLUSTERNAME` substituindo pelo nome do cluster HDInsight. Em seguida, introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Configure a variável de ambiente quando uma conexão SSH tiver sido estabelecida. Substitua `FILE_NAME`, `SQL_SERVERNAME`, ,`SQL_DATABASE` epelos`SQL_PASWORD` valores apropriados`SQL_USER`. Em seguida, digite o comando:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Descompacte o arquivo. zip digitando o comando a seguir:

    ```bash
    unzip $FILENAME.zip
    ```

5. Crie um diretório no armazenamento do HDInsight e copie o arquivo. csv para o diretório digitando o comando a seguir:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Utilizar uma consulta do Hive para transformar os dados

Existem muitas formas de executar um trabalho do Hive num cluster do HDInsight. Nesta seção, você usará [beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) para executar um trabalho do hive. Para obter informações sobre outros métodos de execução de um trabalho do hive, consulte [usar o Apache Hive no HDInsight](../hadoop/hdinsight-use-hive.md).

Como parte do trabalho do Hive, importe os dados do ficheiro .csv para uma tabela do Hive com o nome **Delays** (Atrasos).

1. No prompt do SSH que você já tem para o cluster HDInsight, use o comando a seguir para criar e editar um novo arquivo chamado **flightdelays. HQL**:

    ```bash
    nano flightdelays.hql
    ```

2. Utilize o seguinte texto como o conteúdo desse ficheiro:

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
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
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

3. Para salvar o arquivo, pressione **Ctrl + X**, então **y**e, em seguida, Enter.

4. Para iniciar o Hive e executar o ficheiro **flightdelays.hql**, utilize o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Quando a execução do script **flightdelays.hql** for concluída, utilize o seguinte comando para abrir uma sessão interativa do Beeline:

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

    Esta consulta obtém uma lista de cidades em que os voos se atrasaram devido às condições atmosféricas, juntamente com o tempo de atraso médio e guarda-a em `/tutorials/flightdelays/output`. Depois, o Sqoop lê os dados a partir dessa localização e exporta-os para a Base de Dados SQL do Azure.

7. Para sair do Beeline, introduza `!quit` na linha de comandos.

## <a name="create-a-sql-database-table"></a>Criar uma base de dados SQL

Existem muitas formas de ligar à Base de Dados SQL e criar uma tabela. Os passos seguintes utilizam [FreeTDS](http://www.freetds.org/) do cluster do HDInsight.

1. Para instalar o FreeTDS, use o seguinte comando da conexão SSH aberta para o cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Após a conclusão da instalação, utilize o seguinte comando para ligar ao servidor da Base de Dados SQL.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Receberá um resultado semelhante ao seguinte texto:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Na linha de comandos `1>`, introduza as seguintes linhas:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Essa instrução cria uma tabela chamada **atrasos**, com um índice clusterizado.

    Utilize a seguinte consulta para verificar se a tabela foi criada:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    O resultado é semelhante ao seguinte texto:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Introduza `exit` na linha de comandos `1>` para sair do utilitário tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Exportar dados para o banco de dado SQL usando o Apache Sqoop

Nas secções anteriores, copiou os dados transformados em `/tutorials/flightdelays/output`. Nesta secção, vai utilizar o Sqoop para exportar os dados de `/tutorials/flightdelays/output` para a tabela que criou na base de dados SQL do Azure.

1. Verifique se o Sqoop pode ver o banco de dados SQL inserindo o comando a seguir:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Esse comando retorna uma lista de bancos de dados, incluindo o banco de dados no qual você `delays` criou a tabela anteriormente.

2. Exporte dados `/tutorials/flightdelays/output` do para `delays` a tabela inserindo o comando a seguir:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    O Sqoop conecta-se ao banco de `delays` dados `/tutorials/flightdelays/output` que contém a tabela e exporta o diretório para `delays` a tabela.

3. Depois que o comando sqoop for concluído, use o utilitário TSQL para se conectar ao banco de dados digitando o comando a seguir:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Utilize as instruções seguintes para verificar se os dados foram exportados para a tabela de atrasos:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Deverá ver uma lista dos dados na tabela. A tabela inclui o nome da cidade e o tempo médio dos atrasos dos voos. 

    Introduza `exit` para sair do utilitário tsql.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você tirou um arquivo de dados CSV bruto, importou-o para um armazenamento de cluster HDInsight e, em seguida, transformou os dados usando a consulta interativa no Azure HDInsight.  Avance para o próximo tutorial para saber mais sobre o conector do Apache Hive warehouse.

> [!div class="nextstepaction"]
>[Integrar Apache Spark e Apache Hive com o conector do depósito do hive](./apache-hive-warehouse-connector.md)
