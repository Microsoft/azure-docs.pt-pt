---
title: 'Tutorial: Extrair, transformar e carregar dados utilizando a Azure HDInsight'
description: Neste tutorial, aprende-se a extrair dados de um conjunto de dados CSV bruto, transforma-os utilizando a Hive Apache no Azure HDInsight e, em seguida, carrega os dados transformados em Base de Dados SQL Azure utilizando o Sqoop.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: b247a72b5d7db9892c6a2a763b7b71dc5f972d95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045302"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados utilizando a Azure HDInsight

Neste tutorial, realiza-se uma operação ETL: extrair, transformar e carregar dados. Pega-se num ficheiro de dados CSV bruto, importa-se num cluster Azure HDInsight, transforma-o com a Hive Apache e coloca-o na Base de Dados Azure SQL com o Apache Sqoop.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Extrair e carregar os dados para um cluster HDInsight.
> * Transforme os dados usando a Colmeia Apache.
> * Carregue os dados para a Base de Dados Azure SQL utilizando o Sqoop.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta de armazenamento Azure Data Lake Storage Gen2 que está configurada para HDInsight**

    Consulte [a Utilização Azure Data Lake Storage Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Um cluster Hadoop baseado em Linux em HDInsight**

    Ver [Quickstart: Começar com Apache Hadoop e Apache Hive em Azure HDInsight usando o portal Azure](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Base de Dados Azure SQL**: Utiliza a Base de Dados Azure SQL como uma loja de dados de destino. Se não tiver uma base de dados na Base de Dados SQL, consulte [criar uma base de dados na Base de Dados Azure SQL no portal Azure](../../sql-database/sql-database-get-started.md).

* **Azure CLI**: Se ainda não instalou o Azure CLI, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Um cliente Secure Shell (SSH):** Para obter mais informações, consulte [Connect to HDInsight (Hadoop) utilizando sSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Transferir os dados de voos

1. Navegue para [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Administração de Investigação e Inovação Tecnológica, Instituto de Estatísticas de Transportes).

2. Na página, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar Ano |2013 |
   | Filtrar Período |Janeiro |
   | Campos |Ano, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelayMinutes, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Limpe todos os outros campos.

3. Selecione **Transferir**. Vai obter um ficheiro .zip com os campos de dados que selecionou.

## <a name="extract-and-upload-the-data"></a>Extrair e carregar os dados

Nesta secção, você irá enviar dados para o seu cluster HDInsight e, em seguida, copiar esses dados para a sua conta Dese de Armazenamento de Dados Gen2.

1. Abra uma solicitação de comando e use o seguinte comando Secure Copy (Scp) para carregar o ficheiro .zip para o nó de cabeça do cluster HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Substitua o `<file-name>` espaço reservado pelo nome do ficheiro .zip.
   * Substitua o `<ssh-user-name>` espaço reservado pelo login SSH para o cluster HDInsight.
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

   O comando extrai um ficheiro **.csv.**

4. Utilize o seguinte comando para criar o recipiente Data Lake Storage Gen2.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Substitua o `<container-name>` espaço reservado pelo nome que pretende dar ao seu recipiente.

   Substitua o `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

5. Utilize o seguinte comando para criar um diretório.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Utilize o seguinte comando para copiar o ficheiro *.csv* para o diretório:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Utilize aspas em torno do nome do ficheiro se o nome do ficheiro contiver espaços ou caracteres especiais.

## <a name="transform-the-data"></a>Transformar os dados

Nesta secção, você usa Beeline para dirigir um trabalho de Apache Hive.

Como parte do trabalho da Apache Hive, você importa os dados do ficheiro .csv para uma tabela apache colmeia chamada **atrasos**.

1. A partir do pedido SSH que já tem para o cluster HDInsight, use o seguinte comando para criar e editar um novo ficheiro chamado     **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. Modifique o texto seguinte substituindo os `<container-name>` espaços reservados e `<storage-account-name>` os espaços reservados pelo nome da sua conta de contentor e armazenamento. Em seguida, copie e cole o texto na consola nano, premindo a tecla SHIFT juntamente com o botão de clique do rato direito.

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

3. Guarde o ficheiro utilizando o CTRL+X e escreva `Y` quando solicitado.

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

Precisa do nome do servidor da Base de Dados SQL para esta operação. Complete estes passos para encontrar o nome do seu servidor.

1. Aceda ao [portal do Azure](https://portal.azure.com).

2. Selecione **bases de dados SQL**.

3. Filtrar o nome da base de dados que escolhe utilizar. O nome do servidor é mostrado na coluna **Nome do servidor**.

4. Filtre o nome da base de dados que pretende utilizar. O nome do servidor é mostrado na coluna **Nome do servidor**.

    ![Obtenha detalhes do servidor Azure SQL](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obtenha detalhes do servidor Azure SQL")

    Existem muitas formas de ligar à Base de Dados SQL e criar uma tabela. Os passos seguintes utilizam [FreeTDS](https://www.freetds.org/) do cluster do HDInsight.

5. Para instalar o FreeTDS, utilize o seguinte comando a partir de uma ligação SSH ao cluster:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Após a conclusão da instalação, utilize o seguinte comando para ligar à Base de Dados SQL.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Substitua o `<server-name>` espaço reservado pelo nome lógico do servidor SQL.

   * Substitua o `<admin-login>` espaço reservado pelo login de administração para a Base de Dados SQL.

   * Substitua o `<database-name>` espaço reservado pelo nome da base de dados

   Quando for solicitado, insira a palavra-passe para o login de administração sql Database.

   Receberá um resultado semelhante ao seguinte texto:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Na `1>` origem, insira as seguintes declarações:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas.

   A consulta cria uma tabela chamada **delays**, que tem um índice agrupado.

9. Utilize a seguinte consulta para verificar se a tabela foi criada:

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

Nas secções anteriores, copiou os dados transformados no  `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` local. Nesta secção, utiliza o Sqoop para exportar os dados `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` para a tabela que criou na Base de Dados Azure SQL.

1. Utilize o seguinte comando para verificar se o Sqoop pode ver a sua base de dados SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   O comando devolve uma lista de bases de dados, incluindo a base de dados na qual criou a tabela **de atrasos.**

2. Utilize o seguinte comando para exportar dados da tabela **hivesmpletable** para a tabela **de atrasos:**

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   A Sqoop liga-se à base de dados que contém a tabela de atrasos e exporta **dados** do `/tutorials/flightdelays/output` diretório para a tabela de **atrasos.**

3. Após o acabamento do `sqoop` comando, utilize o utilitário tsql para ligar à base de dados:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Utilize as seguintes declarações para verificar se os dados foram exportados para o quadro **de atrasos:**

   ```sql
   SELECT * FROM delays
   GO
   ```

   Deverá ver uma lista dos dados na tabela. A tabela inclui o nome da cidade e o tempo médio dos atrasos dos voos.

5. Entre `exit` para sair da utilidade tsql.

## <a name="clean-up-resources"></a>Limpar os recursos

Todos os recursos utilizados neste tutorial são pré-existentes. Não é necessária limpeza.

## <a name="next-steps"></a>Passos seguintes

Para aprender mais formas de trabalhar com os dados em HDInsight, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
