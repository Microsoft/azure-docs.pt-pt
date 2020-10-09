---
title: 'Tutorial: Operações ETL com Consulta Interativa - Azure HDInsight'
description: Tutorial - Saiba como extrair dados de um conjunto de dados CSV bruto. Transforme-o com consulta interativa em HDInsight. Em seguida, carregue os dados transformados em Azure SQL Database utilizando o Apache Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 5c5a3c9e66a4d25a84d7940f49ec332d57f4c818
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85319196"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando Consulta Interativa em Azure HDInsight

Neste tutorial, você descarrega um ficheiro de dados CSV bruto de dados de voo disponíveis ao público. Importe-o em armazenamento de cluster HDInsight e, em seguida, transforme os dados usando a Consulta Interativa em Azure HDInsight. Assim que os dados são transformados, você carrega esses dados numa base de dados na Base de Dados Azure SQL usando [Apache Sqoop](https://sqoop.apache.org/).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Transferir os dados de voos de exemplo
> * Carregar os dados para um cluster do HDInsight
> * Transforme os dados usando consulta interativa
> * Criar uma tabela numa base de dados na Base de Dados Azure SQL
> * Utilize o Sqoop para exportar dados para uma base de dados na Base de Dados Azure SQL

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de consultainternação em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Consulta Interativa** para **o tipo cluster**.

* Uma base de dados na Base de Dados Azure SQL. Utiliza a base de dados como uma loja de dados de destino. Se não tiver uma base de dados na Base de Dados Azure SQL, consulte [criar uma base de dados na Base de Dados Azure SQL no portal Azure](/azure/sql-database/sql-database-single-database-get-started).

* Um cliente SSH. Para obter mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Navegue para [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Administração de Investigação e Inovação Tecnológica, Instituto de Estatísticas de Transportes).

2. Na página, limpe todos os campos e, em seguida, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar Ano |2019 |
   | Filtrar Período |Janeiro |
   | Campos |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Carregar os dados para um cluster do HDInsight

Existem muitas formas de carregar dados para o armazenamento associado a um cluster do HDInsight. Nesta secção, vai utilizar `scp` para carregar os dados. Para ver outras formas de carregar dados, veja [Upload data to HDInsight](../hdinsight-upload-data.md) (Carregar dados para o HDInsight).

1. Faça o upload do ficheiro .zip para o nó de cabeça do cluster HDInsight. Edite o comando abaixo substituindo-o pelo `FILENAME` nome do ficheiro .zip e pelo nome `CLUSTERNAME` do cluster HDInsight. Em seguida, abra um pedido de comando, desabrocha o seu diretório de trabalho para o local do ficheiro e, em seguida, insira o comando.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Insira sim ou não para continuar se solicitado. O texto não é visível na janela como se escreve.

2. Depois de concluído o carregamento, utilize SSH para ligar ao cluster. Edite o comando abaixo substituindo-o pelo `CLUSTERNAME` nome do cluster HDInsight. Em seguida, introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Configurar a variável ambiente uma vez estabelecida uma ligação SSH. `FILE_NAME`Substitua, , , , e pelos `SQL_SERVERNAME` `SQL_DATABASE` `SQL_USER` `SQL_PASWORD` valores apropriados. Em seguida, insira o comando:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Desaperte o ficheiro .zip introduzindo o comando abaixo:

    ```bash
    unzip $FILENAME.zip
    ```

5. Crie um diretório no armazenamento HDInsight e, em seguida, copie o ficheiro .csv para o diretório introduzindo o comando abaixo:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Utilizar uma consulta do Hive para transformar os dados

Existem muitas formas de executar um trabalho do Hive num cluster do HDInsight. Nesta secção, você usa [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) para dirigir um trabalho de Colmeia. Para obter informações sobre outros métodos de execução de um trabalho de Colmeia, consulte [Use Apache Hive em HDInsight](../hadoop/hdinsight-use-hive.md).

Como parte do trabalho do Hive, importe os dados do ficheiro .csv para uma tabela do Hive com o nome **Delays** (Atrasos).

1. A partir do pedido SSH que já tem para o cluster HDInsight, use o seguinte comando para criar, e edite um novo ficheiro chamado **flightdelays.hql**:

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

3. Para guardar o ficheiro, prima **Ctrl + X,** em seguida, **y,** e depois introduza.

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

1. Para instalar o FreeTDS, utilize o seguinte comando a partir da ligação SSH aberta ao cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Após o fim da instalação, utilize o seguinte comando para ligar à Base de Dados SQL.

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

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Esta declaração cria uma tabela chamada **delays,** com um índice agrupado.

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

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Dados de exportação para base de dados SQL usando Apache Sqoop

Nas secções anteriores, copiou os dados transformados em `/tutorials/flightdelays/output`. Nesta secção, utiliza o Sqoop para exportar os dados para a tabela que criou na Base de `/tutorials/flightdelays/output` Dados Azure SQL.

1. Verifique se o Sqoop pode ver a sua base de dados SQL inserindo o comando abaixo:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Este comando devolve uma lista de bases de dados, incluindo a base de dados na qual criou a `delays` tabela anteriormente.

2. Dados de exportação da `/tutorials/flightdelays/output` `delays` tabela, introduzindo o comando abaixo:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop conecta-se à base de dados que contém a `delays` tabela, e exporta dados do `/tutorials/flightdelays/output` diretório para a `delays` mesa.

3. Após o acabamento do comando sqoop, utilize o utilitário tsql para ligar à base de dados introduzindo o comando abaixo:

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

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, pegou num ficheiro de dados CSV bruto, importou-o num armazenamento de cluster HDInsight e depois transformou os dados usando a Interactive Query em Azure HDInsight.  Avance para o próximo tutorial para saber sobre o Conector do Armazém da Colmeia Apache.

> [!div class="nextstepaction"]
> [Integre a Faísca Apache e a Colmeia Apache com o Conector do Armazém da Colmeia](./apache-hive-warehouse-connector.md)
