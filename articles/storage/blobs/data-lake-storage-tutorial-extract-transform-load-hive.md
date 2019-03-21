---
title: 'Tutorial: Realizar a extração, transformação, operações de carregamento (ETL), utilizando o Apache Hive no HDInsight do Azure'
description: Neste tutorial, saiba como extrair dados de um conjunto de dados do CSV não processado, transformá-los com o Apache Hive no Azure HDInsight e, em seguida, carregá-los no banco de dados do Azure SQL com o Sqoop.
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jamesbak
ms.openlocfilehash: cdb89b552d0e328e6685e2bd62ea135e15e4b074
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013444"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados com o Apache Hive no HDInsight do Azure

Neste tutorial, vai realizar uma operação de ETL: extrair, transformar e carregar dados. Pega um arquivo de dados não processado do CSV, importe-o para um cluster do Azure HDInsight, transformá-los com o Apache Hive e carregá-los para uma base de dados SQL do Azure com o Apache Sqoop.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Extrair e carregar os dados para um cluster do HDInsight.
> * Transforme os dados utilizando o Apache Hive.
> * Carregar os dados para uma base de dados SQL do Azure ao utilizar o Sqoop.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake está configurada para HDInsight**

    Ver [Use Azure Data Lake armazenamento Gen2 com o Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Um cluster do Hadoop baseado em Linux no HDInsight**

    Consulte [início rápido: Introdução ao Apache Hadoop e Apache Hive no HDInsight do Azure no portal do Azure](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Base de dados SQL do Azure**: Vai utilizar uma base de dados SQL do Azure como arquivo de dados de destino. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure no portal do Azure](../../sql-database/sql-database-get-started.md).

* **CLI do Azure**: Se ainda não instalou a CLI do Azure, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Um cliente Secure Shell (SSH)**: Para obter mais informações, consulte [ligar ao HDInsight (Hadoop) através de SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Os passos neste artigo necessitam de um cluster do HDInsight que utiliza o Linux. Linux é o único sistema operativo que é utilizado no Azure HDInsight versão 3.4 ou posterior. Para obter mais informações, veja [HDInsight retirement on Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Navegue até [pesquisa e administração de tecnologia inovadora, Bureau de estatísticas de transportes](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na página, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar Ano |2013 |
   | Filtrar Período |Janeiro |
   | Campos |Ano, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, origem, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Limpe todos os outros campos.

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="extract-and-upload-the-data"></a>Extrair e carregar os dados

Nesta secção, irá carregar dados para o seu cluster do HDInsight e, em seguida, copiar esses dados para a sua conta de geração 2 de armazenamento do Data Lake.

1. Abra uma linha de comandos e utilize o seguinte comando de cópia segura (Scp) para carregar o ficheiro. zip para o nó principal do cluster de HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Substitua o `<file-name>` marcador de posição pelo nome do ficheiro. zip.
   * Substitua o `<ssh-user-name>` espaço reservado com o início de sessão SSH para o cluster do HDInsight.
   * Substitua o `<cluster-name>` marcador de posição pelo nome do cluster do HDInsight.

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

   O comando extrai uma **. csv** ficheiro.

4. Utilize o seguinte comando para criar o sistema de ficheiros de geração 2 de armazenamento do Data Lake.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Substitua o `<file-system-name>` marcador de posição pelo nome que pretende dar o seu sistema de ficheiros.

   Substitua o `<storage-account-name>` marcador de posição pelo nome da sua conta de armazenamento.

5. Utilize o seguinte comando para criar um diretório.

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Utilize o comando seguinte para copiar o *. csv* ficheiro para o diretório:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Utilize o nome de ficheiro entre aspas se o nome do ficheiro contiver espaços ou carateres especiais.

## <a name="transform-the-data"></a>Transformar os dados

Nesta secção, vai utilizar Beeline para executar uma tarefa Apache Hive.

Como parte do trabalho do Apache Hive, importar os dados do ficheiro. csv para uma tabela com o nome do Apache Hive **atrasos**.

1. A partir de linha de comandos SSH que já tem para o cluster do HDInsight, utilize o seguinte comando para criar e editar um novo ficheiro designado **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. Modificar o texto seguinte ao substituir a `<file-system-name>` e `<storage-account-name>` marcadores de posição pelo seu nome de conta de armazenamento e de sistema de ficheiro. Em seguida, copie e cole o texto na consola do nano, utilizando a pressionar a tecla SHIFT, juntamente com o botão de clique do mouse à direita.

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
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
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

3. Guarde-o utilizando utilize CTRL + X e, em seguida, escreva `Y` quando lhe for pedido.

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

   Esta consulta obtém uma lista de cidades em que os voos se atrasaram devido às condições atmosféricas, juntamente com o tempo de atraso médio e guarda-a em `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Depois, o Sqoop lê os dados a partir dessa localização e exporta-os para a Base de Dados SQL do Azure.

7. Para sair do Beeline, introduza `!quit` na linha de comandos.

## <a name="create-a-sql-database-table"></a>Criar uma base de dados SQL

É necessário o nome de servidor da base de dados SQL para esta operação. Conclua estes passos para encontrar o nome do servidor.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Selecione **bases de dados SQL**.

3. Filtrar no nome da base de dados que optar por utilizar. O nome do servidor é mostrado na coluna **Nome do servidor**.

4. Filtrar no nome da base de dados que pretende utilizar. O nome do servidor é mostrado na coluna **Nome do servidor**.

    ![Obter os detalhes do servidor SQL do Azure](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obter os detalhes do servidor SQL do Azure")

    Existem muitas formas de ligar à Base de Dados SQL e criar uma tabela. Os passos seguintes utilizam [FreeTDS](http://www.freetds.org/) do cluster do HDInsight.

5. Para instalar o FreeTDS, utilize o seguinte comando a partir de uma ligação SSH ao cluster:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Depois de concluída a instalação, utilize o seguinte comando para ligar ao servidor de base de dados SQL.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Substitua o `<server-name>` marcador de posição pelo nome do servidor de base de dados SQL.

   * Substitua o `<admin-login>` espaço reservado com o início de sessão de administrador para a base de dados SQL.

   * Substitua o `<database-name>` marcador de posição pelo nome da base de dados

   Quando lhe for pedido, introduza a palavra-passe para início de sessão de administrador da base de dados SQL.

   Receberá um resultado semelhante ao seguinte texto:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Na `1>` , introduza as seguintes instruções:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas.

   A consulta cria uma tabela denominada **atrasos**, que tem um índice em cluster.

9. Utilize a seguinte consulta para verificar se a tabela é criada:

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

## <a name="export-and-load-the-data"></a>Exportar e carregar os dados

Nas seções anteriores, que copiou os dados transformados na localização `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Nesta secção, vai utilizar Sqoop para exportar os dados de `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` para a tabela criada na base de dados SQL do Azure.

1. Utilize o seguinte comando para verificar se o Sqoop pode ver a sua base de dados SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   O comando devolve uma lista de bases de dados, incluindo a base de dados em que criou o **atrasos** tabela.

2. Utilize o seguinte comando para exportar dados a partir da **hivesampletable** de tabela para o **atrasos** tabela:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop liga-se à base de dados que contém o **atrasos** tabela e dados de exportações da `/tutorials/flightdelays/output` diretório para o **atrasos** tabela.

3. Depois do `sqoop` comando estiver concluído, utilize o utilitário de tsql para ligar à base de dados:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Utilize as seguintes instruções para verificar que os dados foram exportados para o **atrasos** tabela:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Deverá ver uma lista dos dados na tabela. A tabela inclui o nome da cidade e o tempo médio dos atrasos dos voos.

5. Introduza `exit` para saia do utilitário de tsql.

## <a name="clean-up-resources"></a>Limpar recursos

Todos os recursos utilizados neste tutorial são pré-existentes. Limpeza de não é necessária.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais formas de trabalhar com dados no HDInsight, consulte o artigo seguinte:

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados com o Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
