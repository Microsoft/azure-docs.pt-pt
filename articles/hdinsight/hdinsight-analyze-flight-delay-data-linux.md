---
title: 'Tutorial: Realizar a extração, transformação, operações de carregamento (ETL) utilizando o Hive no HDInsight - Azure '
description: Saiba como extrair dados de um conjunto de dados não processados em CSV, transformá-los com o Hive no HDInsight e, em seguida, carregar os dados transformados na base de dados SQL do Azure com o Apache Sqoop.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: b8a45f3014b3fd5d0f5592b3f9bd408fc37b387b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999949"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados com o Apache Hive no Azure HDInsight

Neste tutorial, pega um arquivo de dados não processado do CSV, importe-o para um armazenamento de cluster do HDInsight e, em seguida, transforme os dados com [Apache Hive](https://hive.apache.org/) no Azure HDInsight. Depois dos dados são transformados, carregar dados para uma base de dados SQL do Azure com [Apache Sqoop](https://sqoop.apache.org/). Neste artigo, vai utilizar dados de voos disponíveis publicamente.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do Azure HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

Este tutorial abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Transferir os dados de voos de exemplo
> * Carregar os dados para um cluster do HDInsight
> * Transformar os dados com o Hive
> * Criar uma tabela na Base de Dados SQL do Azure
> * Utilizar o Sqoop para exportar os dados para a Base de Dados SQL do Azure


A ilustração seguinte mostra um fluxo típico de aplicação ETL.

![Operação de ETL com o Apache Hive no Azure HDInsight](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "Operação de ETL com o Apache Hive no Azure HDInsight")

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do Hadoop baseado em Linux no HDInsight**. Ver [começar a utilizar o Apache Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) para obter passos sobre como criar um novo cluster do HDInsight baseado em Linux.

* **Base de Dados SQL do Azure**. Vai utilizar uma base de dados SQL do Azure como arquivo de dados de destino. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started.md).

* **CLI do Azure**. Se ainda não instalou a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para obter mais passos.

* **Um cliente SSH**. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Navegue para [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (Administração de Investigação e Inovação Tecnológica, Instituto de Estatísticas de Transportes).

2. Na página, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar Ano |2013 |
   | Filtrar Período |Janeiro |
   | Campos |Year (Ano), FlightDate (Data do Voo), UniqueCarrier (Transportadora Individual), Carrier (Transportadora), FlightNum (Número do Voo), OriginAirportID (ID do Aeroporto de Origem), Origin (Origem), OriginCityName (Nome da Cidade de Origem), OriginState (Estado de Origem), DestAirportID (ID do Aeroporto de Destino), Dest (Destino), DestCityName (Nome da Cidade de Destino), DestState (Estado de Destino), DepDelayMinutes (Minutos de Atraso da Partida), ArrDelay (Atraso à Chegada), ArrDelayMinutes (Minutos de Atraso à Chegada), CarrierDelay (Atraso da Transportadora), WeatherDelay (Atraso Devido às Condições Atmosféricas), NASDelay (Atraso Devido à NAS), SecurityDelay (Atraso Devido a Segurança), LateAircraftDelay (Atraso Devido a Avião Atrasado). |
   
   Limpe todos os outros campos. 

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Carregar os dados para um cluster do HDInsight

Existem muitas formas de carregar dados para o armazenamento associado a um cluster do HDInsight. Nesta secção, vai utilizar `scp` para carregar os dados. Para ver outras formas de carregar dados, veja [Upload data to HDInsight](hdinsight-upload-data.md) (Carregar dados para o HDInsight).

1. Abra uma linha de comandos e utilize o seguinte comando para carregar o ficheiro .zip para o nó principal do cluster do HDInsight:

    ```bash
    scp <FILENAME>.zip <SSH-USERNAME>@<CLUSTERNAME>-ssh.azurehdinsight.net:<FILENAME.zip>
    ```

    Substitua *FILENAME* pelo nome do ficheiro .zip. Substitua *USERNAME* pelo início de sessão SSH do cluster do HDInsight. Substitua *CLUSTERNAME* pelo nome do cluster do HDInsight.

   > [!NOTE]  
   > Se utilizar uma palavra-passe para autenticar o início de sessão SSH, é-lhe pedida a palavra-passe. Se utilizar uma chave pública, poderá ter de utilizar o parâmetro `-i` e especificar o caminho para a chave privada correspondente. Por exemplo, `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Depois de concluído o carregamento, utilize SSH para ligar ao cluster. Na linha de comandos, introduza o seguinte comando:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

3. Utilize o seguinte comando para descomprimir o ficheiro .zip:

    ```bash
    unzip FILENAME.zip
    ```

    Este comando extrai um ficheiro .csv que tem, aproximadamente, 60 MB.

4. Utilize os seguintes comandos para criar um diretório no armazenamento do HDInsight e, em seguida, copie o ficheiro .csv para o diretório:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put <FILENAME>.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Utilizar uma consulta do Hive para transformar os dados

Existem muitas formas de executar um trabalho do Hive num cluster do HDInsight. Nesta secção, vai utilizar [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) para executar uma tarefa do Hive. Para informações sobre outros métodos de executar uma tarefa do Hive, veja [utilizar o Apache Hive no HDInsight](./hadoop/hdinsight-use-hive.md).

Como parte do trabalho do Hive, importe os dados do ficheiro .csv para uma tabela do Hive com o nome **Delays** (Atrasos).

1. Na linha de comandos SSH que já tem para o cluster do HDInsight, utilize o seguinte comando para criar e editar um ficheiro novo designado **flightdelays.hql**:

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

2. Para guardar o ficheiro, prima **Esc** e introduza `:x`.

3. Para iniciar o Hive e executar o ficheiro **flightdelays.hql**, utilize o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Quando a execução do script __flightdelays.hql__ for concluída, utilize o seguinte comando para abrir uma sessão interativa do Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Quando receber a linha de comandos `jdbc:hive2://localhost:10001/>`, utilize a seguinte consulta para obter dados a partir dos dados importados de atrasos de voos:

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

6. Para sair do Beeline, introduza `!quit` na linha de comandos.

## <a name="create-a-sql-database-table"></a>Criar uma base de dados SQL

Esta secção pressupõe que já criou uma base de dados SQL do Azure. Se ainda não a tiver, utilize as informações em [Criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started.md) para criá-la.

Se já tiver uma base de dados SQL, tem de obter o nome do servidor. Para localizar o nome do servidor no [portal do Azure](https://portal.azure.com), selecione **Bases de Dados SQL** e filtre o nome da base de dados que quer utilizar. O nome do servidor é mostrado na coluna **Nome do servidor**.

![Obter os detalhes do servidor SQL do Azure](./media/hdinsight-analyze-flight-delay-data-linux/get-azure-sql-server-details.png "Obter os detalhes do servidor SQL do Azure")

> [!NOTE]  
> Existem muitas formas de ligar à Base de Dados SQL e criar uma tabela. Os passos seguintes utilizam [FreeTDS](http://www.freetds.org/) do cluster do HDInsight.


1. Para instalar o FreeTDS, utilize o seguinte comando a partir de uma ligação SSH ao cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Após a conclusão da instalação, utilize o seguinte comando para ligar ao servidor da Base de Dados SQL. Substitua **serverName** pelo nome do servidor da Base de Dados SQL. Substitua **adminLogin** e **adminPassword** pelo início de sessão da Base de Dados SQL. Substitua **databaseName** pelo nome da base de dados.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Quando lhe for pedido, introduza a palavra-passe do início de sessão de administrador da Base de Dados SQL.

    Receberá um resultado semelhante ao seguinte texto:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Na linha de comandos `1>`, introduza as seguintes linhas:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Esta consulta cria uma tabela com o nome **delays** (atrasos), com um índice em cluster.

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

5. Introduza `exit` na linha de comandos `1>` para sair do utilitário tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Exportar dados para a base de dados SQL com o Apache Sqoop

Nas secções anteriores, copiou os dados transformados em `/tutorials/flightdelays/output`. Nesta secção, vai utilizar o Sqoop para exportar os dados de “/tutorials/flightdelays/output” para a tabela que criou na base de dados SQL do Azure. 

1. Utilize o seguinte comando para verificar se o Sqoop pode ver a sua base de dados SQL:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Este comando devolve uma lista de bases de dados, incluindo aquela em que criou a tabela de atrasos anteriormente.

2. Utilize o seguinte comando para exportar os dados de hivesampletable para a tabela de atrasos:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    O Sqoop liga-se à base de dados que contém a tabela de atrasos e exporta os dados do diretório `/tutorials/flightdelays/output` para essa tabela.

3. Após a conclusão do comando sqoop, utilize o utilitário tsql para ligar à base de dados:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Utilize as instruções seguintes para verificar se os dados foram exportados para a tabela de atrasos:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Deverá ver uma lista dos dados na tabela. A tabela inclui o nome da cidade e o tempo médio dos atrasos dos voos. 

    Introduza `exit` para sair do utilitário tsql.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a realizar operações de extração, transformação e carregamento de dados com um cluster do Apache Hadoop no HDInsight. Avance para o próximo tutorial para saber como utilizar o Azure Data Factory para criar clusters do HDInsight Hadoop a pedido.

> [!div class="nextstepaction"]
>[Criar clusters do Apache Hadoop a pedido no HDInsight com o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md)

Para obter mais formas para trabalhar com dados no HDInsight, veja os seguintes artigos:

* [Tutorial: Extrair, transformar e carregar dados com o Apache Hive no HDInsight do Azure](../storage/data-lake-storage/tutorial-extract-transform-load-hive.md)
* [Utilizar o Apache Hive com o HDInsight][hdinsight-use-hive]
* [Utilizar o Apache Pig com o HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para o Apache Hadoop no HDInsight][hdinsight-develop-mapreduce]
* [Develop Python streaming MapReduce programs for HDInsight][hdinsight-develop-streaming] (Desenvolver programas MapReduce de transmissão em fluxo em Python para o HDInsight)
* [Utilizar o Apache Oozie com o HDInsight][hdinsight-use-oozie]
* [Utilizar o Apache Sqoop com o HDInsight][hdinsight-use-sqoop]



[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/


[rita-website]: https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
