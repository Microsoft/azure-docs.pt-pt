---
title: 'Tutorial: Executar operações de extração, transformação, carregamento (ETL) usando Apache Hive no Azure HDInsight'
description: Neste tutorial, você aprenderá a extrair dados de um conjunto de um CSV bruto, transformá-lo usando Apache Hive no Azure HDInsight e, em seguida, carregar os dados transformados no banco de dado SQL do Azure usando o Sqoop.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f58785b17a1e6236636744c32dac07a6c9ed138d
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992257"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando Apache Hive no Azure HDInsight

Neste tutorial, você executa uma operação de ETL: extrair, transformar e carregar dados. Você pega um arquivo de dados CSV bruto, importa-o para um cluster do Azure HDInsight, transforma-o com Apache Hive e o carrega em um banco de dados SQL do Azure com o Apache Sqoop.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Extraia e carregue os dados em um cluster HDInsight.
> * Transforme os dados usando Apache Hive.
> * Carregue os dados em um banco de dado SQL do Azure usando o Sqoop.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta de armazenamento Azure Data Lake Storage Gen2 configurada para o HDInsight**

    Consulte [usar Azure data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Um cluster Hadoop baseado em Linux no HDInsight**

    Consulte [início rápido: Introdução ao Apache Hadoop e Apache Hive no Azure HDInsight usando o portal do Azure](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Banco de dados SQL do Azure**: Vai utilizar uma base de dados SQL do Azure como arquivo de dados de destino. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL do Azure no portal do Azure](../../sql-database/sql-database-get-started.md).

* **CLI do Azure**: Se você ainda não instalou o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Um cliente Secure Shell (SSH)** : Para obter mais informações, consulte [conectar-se ao HDInsight (Hadoop) usando SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Navegue até [pesquisa e administração inovadora de tecnologia, Bureau de estatísticas de transporte](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na página, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar Ano |2013 |
   | Filtrar Período |Janeiro |
   | Campos |Year, voo, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, origem, OriginCityName, OriginState, DestAirportID, dest, DestCityName, Deststate, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Limpe todos os outros campos.

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="extract-and-upload-the-data"></a>Extrair e carregar os dados

Nesta seção, você carregará dados para o cluster HDInsight e, em seguida, copiará esses dados para sua conta de Data Lake Storage Gen2.

1. Abra um prompt de comando e use o seguinte comando de cópia segura (SCP) para carregar o arquivo. zip no nó principal do cluster HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Substitua o `<file-name>` espaço reservado pelo nome do arquivo. zip.
   * Substitua o `<ssh-user-name>` espaço reservado pelo logon SSH para o cluster HDInsight.
   * Substitua o `<cluster-name>` espaço reservado pelo nome do cluster HDInsight.

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

   O comando extrai um arquivo **. csv** .

4. Use o comando a seguir para criar o contêiner de Data Lake Storage Gen2.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Substitua o `<container-name>` espaço reservado pelo nome que você deseja dar ao seu contêiner.

   Substitua o `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

5. Use o comando a seguir para criar um diretório.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Use o comando a seguir para copiar o arquivo *. csv* para o diretório:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Use aspas em volta do nome do arquivo se o nome do arquivo contiver espaços ou caracteres especiais.

## <a name="transform-the-data"></a>Transformar os dados

Nesta seção, você usará beeline para executar um trabalho de Apache Hive.

Como parte do trabalho de Apache Hive, você importa os dados do arquivo. csv para uma tabela Apache Hive chamada **atrasos**.

1. No prompt do SSH que você já tem para o cluster HDInsight, use o seguinte comando para criar e editar um novo arquivo chamado **flightdelays. HQL**:

   ```bash
   nano flightdelays.hql
   ```

2. Modifique o texto a seguir substituindo `<container-name>` os `<storage-account-name>` espaços reservados e pelo seu contêiner e o nome da conta de armazenamento. Em seguida, copie e cole o texto no console do nano usando pressionar a tecla SHIFT junto com o botão de clique com o botão direito do mouse.

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

3. Salve o arquivo usando Ctrl + X e digite `Y` quando solicitado.

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

Você precisa do nome do servidor do banco de dados SQL para esta operação. Conclua estas etapas para localizar o nome do servidor.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Selecione **bancos de dados SQL**.

3. Filtre o nome do banco de dados que você escolher usar. O nome do servidor é mostrado na coluna **Nome do servidor**.

4. Filtre o nome do banco de dados que você deseja usar. O nome do servidor é mostrado na coluna **Nome do servidor**.

    ![Obter os detalhes do servidor SQL do Azure](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obter os detalhes do servidor SQL do Azure")

    Existem muitas formas de ligar à Base de Dados SQL e criar uma tabela. Os passos seguintes utilizam [FreeTDS](https://www.freetds.org/) do cluster do HDInsight.

5. Para instalar o FreeTDS, utilize o seguinte comando a partir de uma ligação SSH ao cluster:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Após a conclusão da instalação, use o comando a seguir para se conectar ao servidor do banco de dados SQL.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Substitua o `<server-name>` espaço reservado pelo nome do servidor do banco de dados SQL.

   * Substitua o `<admin-login>` espaço reservado pelo logon de administrador do banco de dados SQL.

   * Substitua o `<database-name>` espaço reservado pelo nome do banco de dados

   Quando for solicitado, insira a senha para o logon de administrador do banco de dados SQL.

   Receberá um resultado semelhante ao seguinte texto:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. `1>` No prompt, insira as seguintes instruções:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas.

   A consulta cria uma tabela chamada **atrasos**, que tem um índice clusterizado.

9. Use a consulta a seguir para verificar se a tabela foi criada:

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

Nas seções anteriores, você copiou os dados transformados no local `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Nesta seção, você usará Sqoop para exportar os dados do `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` para a tabela que você criou no banco de dados SQL do Azure.

1. Utilize o seguinte comando para verificar se o Sqoop pode ver a sua base de dados SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   O comando retorna uma lista de bancos de dados, incluindo o Database no qual você criou a tabela de atrasos.

2. Use o comando a seguir para exportar dados da tabela **hivesampletable** para a tabela **atrasos** :

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop conecta-se ao banco de dados que contém a tabela de atrasos e `/tutorials/flightdelays/output` exporta o diretório para a tabela de **atrasos** .

3. Depois que `sqoop` o comando for concluído, use o utilitário TSQL para se conectar ao banco de dados:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Use as instruções a seguir para verificar se os dados foram exportados para a tabela de **atrasos** :

   ```sql
   SELECT * FROM delays
   GO
   ```

   Deverá ver uma lista dos dados na tabela. A tabela inclui o nome da cidade e o tempo médio dos atrasos dos voos.

5. Insira `exit` para sair do utilitário TSQL.

## <a name="clean-up-resources"></a>Limpar recursos

Todos os recursos usados neste tutorial são preexistentes. Nenhuma limpeza é necessária.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais maneiras de trabalhar com dados no HDInsight, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
