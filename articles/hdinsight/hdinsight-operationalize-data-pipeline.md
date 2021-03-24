---
title: Operacionalizar um pipeline de análise de dados - Azure
description: Crie e execute um pipeline de dados de exemplo que é desencadeado por novos dados e produz resultados concisos.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: c81eb092fa59cb890093e1e9acd0511e39b5047b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864215"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacionalizar um pipeline de análise de dados

*Os oleodutos de dados* são menos soluções de análise de dados. Como o nome sugere, um pipeline de dados leva dados brutos, limpa e remodela-os conforme necessário, e depois normalmente realiza cálculos ou agregações antes de armazenar os dados tratados. Os dados processados são consumidos por clientes, relatórios ou APIs. Um gasoduto de dados deve fornecer resultados repetíveis, seja num horário ou quando desencadeado por novos dados.

Este artigo descreve como operacionalizar os seus oleodutos de dados para a repetibilidade, utilizando o Oozie em execução em clusters HDInsight Hadoop. O cenário de exemplo acompanha-o através de um pipeline de dados que prepara e processa dados da série de voos da companhia aérea.

No cenário seguinte, os dados de entrada são um ficheiro plano contendo um lote de dados de voo durante um mês. Estes dados de voo incluem informações como a origem e o aeroporto de destino, as milhas voadas, os horários de partida e chegada, etc. O objetivo com este oleoduto é resumir o desempenho diário da companhia aérea, onde cada companhia aérea tem uma linha para cada dia com os atrasos médios de partida e chegada em minutos, e o total de milhas voadas nesse dia.

| YEAR | MONTH | DAY_OF_MONTH | TRANSPORTADORA |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

O pipeline exemplo aguarda até que cheguem os dados de voo de um novo período de tempo, em seguida, armazena essa informação detalhada de voo no seu armazém de dados Apache Hive para análises a longo prazo. O oleoduto também cria um conjunto de dados muito menor que resume apenas os dados diários de voo. Estes dados de resumo diário de voo são enviados para uma Base de Dados SQL para fornecer relatórios, tais como para um website.

O diagrama que se segue ilustra o gasoduto exemplo.

:::image type="content" source="./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png" alt-text="Visão geral do pipeline de dados de exemplo de voo HDI" border="false":::

## <a name="apache-oozie-solution-overview"></a>Visão geral da solução Apache Oozie

Este oleoduto utiliza Apache Oozie a funcionar num cluster HDInsight Hadoop.

A Oozie descreve os seus oleodutos em termos de *ações,* *fluxos de trabalho* e *coordenadores.* As ações determinam o trabalho real a realizar, como executar uma consulta de Colmeia. Os fluxos de trabalho definem a sequência de ações. Os coordenadores definem o horário para quando o fluxo de trabalho é executado. Os coordenadores também podem aguardar a disponibilidade de novos dados antes de lançar uma instância do fluxo de trabalho.

O diagrama que se segue mostra o design de alto nível deste pipeline exemplo Oozie.

:::image type="content" source="./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png" alt-text="Pipeline de dados de exemplo de voo Oozie" border="false":::

## <a name="provision-azure-resources"></a>Fornecimento de recursos Azure

Este oleoduto requer uma Base de Dados Azure SQL e um cluster HDInsight Hadoop no mesmo local. A Base de Dados Azure SQL armazena tanto os dados sumários produzidos pelo pipeline como a Oozie Metadata Store.

### <a name="provision-azure-sql-database"></a>Provisão Azure SQL Database

1. Criar uma Base de Dados Azure SQL. Consulte [a Criação de uma Base de Dados Azure SQL no portal Azure](../azure-sql/database/single-database-create-quickstart.md).

1. Para garantir que o seu cluster HDInsight pode aceder à base de dados Azure SQL conectada, configurar as regras de firewall da Base de Dados Azure SQL para permitir que os serviços e recursos da Azure possam aceder ao servidor. Pode ativar esta opção no portal Azure selecionando a **firewall do servidor set** e selecionando **ON** por **baixo, permitir que os serviços e recursos do Azure acedam a este servidor** para Azure SQL Database. Para obter mais informações, consulte [Criar e gerir as regras de firewall IP](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Utilize [o editor de consulta](../azure-sql/database/single-database-create-quickstart.md#query-the-database) para executar as seguintes declarações SQL para criar a tabela que `dailyflights` armazenará os dados resumidos de cada execução do pipeline.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

A sua Base de Dados Azure SQL está agora pronta.

### <a name="provision-an-apache-hadoop-cluster"></a>Provisão de um Aglomerado Apache Hadoop

Crie um cluster Apache Hadoop com uma metástaia personalizada. Durante a criação do cluster a partir do portal, a partir do **separador Armazenamento,** certifique-se de que seleciona a sua Base de Dados SQL nas **definições de Metastore**. Para obter mais informações sobre a seleção de uma meta-loja, consulte [selecione uma meta-loja personalizada durante a criação do cluster](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Para obter mais informações sobre a criação de clusters, consulte [Get Started with HDInsight on Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Verificar a instalação de túneis SSH

Para utilizar a Consola Web Oozie para visualizar o estado do seu coordenador e instâncias de fluxo de trabalho, crie um túnel SSH para o seu cluster HDInsight. Para mais informações, consulte [o Túnel SSH.](hdinsight-linux-ambari-ssh-tunnel.md)

> [!NOTE]  
> Também pode utilizar o Chrome com a extensão [Foxy Proxy](https://getfoxyproxy.org/) para navegar nos recursos web do seu cluster através do túnel SSH. Configuure-o para proxy todo o pedido através do hospedeiro `localhost` no porto do túnel 9876. Esta abordagem é compatível com o Subsistema Windows para Linux, também conhecido como Bash no Windows 10.

1. Executar o seguinte comando para abrir um túnel SSH para o seu aglomerado, onde `CLUSTERNAME` está o nome do seu cluster:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verifique se o túnel está operacional navegando para Ambari no nó da cabeça navegando para:

    `http://headnodehost:8080`

1. Para aceder à **Consola Web Oozie** a partir de Ambari, navegue para **Oozie**  >  **Quick Links** > [servidor ativo] > **Oozie Web UI**.

## <a name="configure-hive"></a>Colmeia Configure

### <a name="upload-data"></a>Carregar dados

1. Faça o download de um ficheiro CSV de exemplo que contenha dados de voo durante um mês. Descarregue o seu ficheiro ZIP `2017-01-FlightData.zip` do [repositório HDInsight GitHub](https://github.com/hdinsight/hdinsight-dev-guide) e desaperte-o para o ficheiro CSV `2017-01-FlightData.csv` .

1. Copie este ficheiro CSV até à conta de Armazenamento Azure anexado ao seu cluster HDInsight e coloque-o na `/example/data/flights` pasta.

    1. Utilize o SCP para copiar os ficheiros da sua máquina local para o armazenamento local do seu nó de cabeça de cluster HDInsight.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do seu cluster e, em seguida, insira o comando:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. A partir da sessão de sessão, utilize o comando HDFS para copiar o ficheiro do seu nó de cabeça para o armazenamento local do Azure.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Criar tabelas

Os dados da amostra estão agora disponíveis. No entanto, o gasoduto requer duas tabelas de Colmeia para processamento, uma para os dados recebidos ( `rawFlights` ) e uma para os dados resumidos ( `flights` ). Crie estas mesas em Ambari da seguinte forma.

1. Faça login em Ambari navegando para `http://headnodehost:8080` .

2. Da lista de serviços, **selecione Hive.**

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png" alt-text="Lista de serviços da Apache Ambari que seleciona a Colmeia":::

3. Selecione **Ir Para Ver** ao lado da etiqueta Hive View 2.0.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png" alt-text="Lista de resumos da Colmeia Ambari Apache":::

4. Na área de texto de consulta, cole as seguintes declarações para criar a `rawFlights` tabela. A `rawFlights` tabela fornece um esquema de leitura para os ficheiros CSV dentro da pasta no `/example/data/flights` Azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. **Selecione Executar** para criar a tabela.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png" alt-text="hdi ambari serviços consulta colmeia":::

6. Para criar a `flights` tabela, substitua o texto na área de texto de consulta pelas seguintes declarações. A `flights` tabela é uma tabela gerida pela Colmeia que partilha dados carregados nele por ano, mês e dia do mês. Esta tabela conterá todos os dados históricos de voo, com a menor granularidade presente nos dados de origem de uma linha por voo.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. **Selecione Executar** para criar a tabela.

## <a name="create-the-oozie-workflow"></a>Criar o fluxo de trabalho Oozie

Os gasodutos normalmente processam dados em lotes por um intervalo de tempo. Neste caso, o oleoduto processa os dados de voo diariamente. Esta abordagem permite que os ficheiros CSV de entrada cheguem diariamente, semanalmente, mensalmente ou anualmente.

O fluxo de trabalho da amostra processa os dados de voo dia-a-dia, em três passos principais:

1. Faça uma consulta de Hive para extrair os dados para a data desse dia variam a partir do ficheiro CSV de origem representado pela `rawFlights` tabela e insira os dados na `flights` tabela.
2. Executar uma consulta de Colmeia para criar dinamicamente uma tabela de preparação na Colmeia para o dia, que contém uma cópia dos dados de voo resumidos por dia e transportadora.
3. Utilize o Apache Sqoop para copiar todos os dados da tabela de preparação diária na Colmeia até à tabela de destino na Base de `dailyflights` Dados Azure SQL. Sqoop lê as linhas de origem a partir dos dados por trás da tabela Hive que reside no Armazenamento Azure e os carrega na Base de Dados SQL usando uma ligação JDBC.

Estes três passos são coordenados por um fluxo de trabalho Oozie.

1. A partir do seu posto de trabalho local, crie um ficheiro chamado `job.properties` . Utilize o texto abaixo como conteúdo inicial para o ficheiro.
Em seguida, atualize os valores para o seu ambiente específico. A tabela abaixo do texto resume cada uma das propriedades e indica onde pode encontrar os valores para o seu próprio ambiente.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Propriedade | Fonte de valor |
    | --- | --- |
    | nomeNode | O caminho completo para o recipiente de armazenamento Azure ligado ao seu cluster HDInsight. |
    | jobTracker | O nome de hospedeiro interno do nó de cabeça yarn do seu cluster ativo. Na página inicial do Ambari, selecione YARN da lista de serviços e, em seguida, escolha Ative Resource Manager. O nome de anfitrião URI é exibido no topo da página. Apêndi-lo o porto 8050. |
    | nome de fila | O nome da fila YARN usado ao agendar as ações da Colmeia. Não altere a predefinição. |
    | oozie.use.system.libpath | Deixe como verdade. |
    | appBase | O caminho para a sub-página no Azure Storage onde implementa o fluxo de trabalho Oozie e ficheiros de suporte. |
    | oozie.wf.application.path | A localização do fluxo de trabalho de Oozie `workflow.xml` para executar. |
    | hiveScriptLoadPartition | O caminho no Azure Storage para o ficheiro de consulta da `hive-load-flights-partition.hql` Colmeia. |
    | hiveScriptCreateDailyTable | O caminho no Azure Storage para o ficheiro de consulta da `hive-create-daily-summary-table.hql` Colmeia. |
    | hiveDailyTableName | O nome gerado dinamicamente para usar para a mesa de preparação. |
    | hiveDataFolder | O caminho no Azure Storage para os dados contidos pela tabela de preparação. |
    | sqlDatabaseConnectionString | O fio de ligação à sintaxe JDBC à sua Base de Dados Azure SQL. |
    | sqlDatabaseTableName | O nome da tabela na Base de Dados Azure SQL na qual são inseridas linhas resumidas. Deixe como `dailyflights` . |
    | ano | A componente do ano do dia para o qual são calculados os resumos dos voos. Deixe como está. |
    | month | A componente mensal do dia para o qual são calculados os resumos dos voos. Deixe como está. |
    | day | A componente do dia do dia para o qual são calculados os resumos dos voos. Deixe como está. |

1. A partir do seu posto de trabalho local, crie um ficheiro chamado `hive-load-flights-partition.hql` . Utilize o código abaixo como o conteúdo do ficheiro.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
          FL_DATE,
          CARRIER,
          FL_NUM,
          ORIGIN,
          DEST,
          DEP_DELAY,
          ARR_DELAY,
          ACTUAL_ELAPSED_TIME,
          DISTANCE,
        YEAR,
          MONTH,
          DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    As variáveis Oozie usam a `${variableName}` sintaxe. Estas variáveis estão definidas no `job.properties` ficheiro. Oozie substitui os valores reais no tempo de execução.

1. A partir do seu posto de trabalho local, crie um ficheiro chamado `hive-create-daily-summary-table.hql` . Utilize o código abaixo como o conteúdo do ficheiro.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
          MONTH INT,
          DAY_OF_MONTH INT,
          CARRIER STRING,
          AVG_DEP_DELAY FLOAT,
          AVG_ARR_DELAY FLOAT,
          TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT     year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Esta consulta cria uma tabela de encenação que armazenará apenas os dados resumidos durante um dia, tome nota da declaração SELECT que calcula os atrasos médios e o total da distância voada por transportadora durante o dia. Os dados inseridos nesta tabela armazenados num local conhecido (o caminho indicado pela variável hiveDataFolder) para que possa ser usado como fonte para o Sqoop no passo seguinte.

1. A partir do seu posto de trabalho local, crie um ficheiro chamado `workflow.xml` . Utilize o código abaixo como o conteúdo do ficheiro. Estes passos acima são expressos como ações separadas no ficheiro de fluxo de trabalho Oozie.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

As duas consultas de Colmeia são acedidas pelo seu caminho no Azure Storage, e os valores variáveis restantes são fornecidos pelo `job.properties` ficheiro. Este ficheiro configura o fluxo de trabalho para a data de 3 de janeiro de 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>Implementar e executar o fluxo de trabalho Oozie

Utilize o SCP da sua sessão de festa para implementar o seu fluxo de trabalho Oozie `workflow.xml` (), as consultas de Hive `hive-load-flights-partition.hql` `hive-create-daily-summary-table.hql` (e) e a configuração de trabalho `job.properties` ().  Em Oozie, só o `job.properties` ficheiro pode existir no armazenamento local do headnode. Todos os outros ficheiros devem ser armazenados em HDFS, neste caso, Azure Storage. A ação Sqoop utilizada pelo fluxo de trabalho depende de um controlador JDBC para comunicar com a sua Base de Dados SQL, que deve ser copiada do nó de cabeça para HDFS.

1. Crie a `load_flights_by_day` sub-página por baixo do caminho do utilizador no armazenamento local do nó de cabeça. A partir da sua sessão aberta, execute o seguinte comando:

    ```bash
    mkdir load_flights_by_day
    ```

1. Copie todos os ficheiros do diretório atual (o `workflow.xml` e `job.properties` os ficheiros) até à `load_flights_by_day` sub-dobra. A partir da sua estação de trabalho local, execute o seguinte comando:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Copiar ficheiros de fluxo de trabalho para HDFS. A partir da sua sessão aberta, execute os seguintes comandos:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Copie `mssql-jdbc-7.0.0.jre8.jar` do nó de cabeça local para a pasta de fluxo de trabalho em HDFS. Reveja o comando se o seu cluster contiver um ficheiro de frasco diferente. Rever `workflow.xml` conforme necessário para refletir um ficheiro de frasco diferente. A partir da sua sessão aberta, execute o seguinte comando:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Executar o fluxo de trabalho. A partir da sua sessão aberta, execute o seguinte comando:

    ```bash
    oozie job -config job.properties -run
    ```

1. Observe o estado utilizando a Consola Web Oozie. De dentro de Ambari, selecione **Oozie,** **Quick Links** e, em seguida, **Oozie Web Console**. No **separador Workflow Jobs,** selecione **All Jobs**.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png" alt-text="hdi oozie web consola fluxos de trabalho":::

1. Quando o estado for BEM SUCEDIDO, consulte a tabela SQL Database para visualizar as linhas inseridas. Utilizando o portal Azure, navegue no painel para a sua Base de Dados SQL, selecione **Ferramentas** e abra o **Editor de Consulta**.

    ```sql
    SELECT * FROM dailyflights
    ```

Agora que o fluxo de trabalho está em execução para o único dia de teste, você pode embrulhar este fluxo de trabalho com um coordenador que agenda o fluxo de trabalho para que funcione diariamente.

## <a name="run-the-workflow-with-a-coordinator"></a>Executar o fluxo de trabalho com um coordenador

Para agendar este fluxo de trabalho para que funcione diariamente (ou todos os dias numa gama de datas), pode utilizar um coordenador. Um coordenador é definido por um ficheiro XML, por `coordinator.xml` exemplo:

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Como pode ver, a maioria do coordenador está apenas a passar informações de configuração para a instância do fluxo de trabalho. No entanto, há alguns itens importantes para chamar.

* Ponto 1: Os `start` `end` atributos e atributos no `coordinator-app` próprio elemento controlam o intervalo de tempo sobre o qual o coordenador corre.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Um coordenador é responsável por agendar ações dentro do `start` intervalo e `end` data, de acordo com o intervalo especificado pelo `frequency` atributo. Cada ação programada, por sua vez, executa o fluxo de trabalho como configurado. Na definição de coordenador acima, o coordenador está configurado para executar ações de 1 de janeiro de 2017 a 5 de janeiro de 2017. A frequência é definida para um dia pela expressão de frequência [da linguagem de expressão Oozie.](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) `${coord:days(1)}` Isto resulta no agendamento de uma ação (e, portanto, do fluxo de trabalho) uma vez por dia. Para os intervalos de datas que estão no passado, como neste exemplo, a ação será agendada para ser executada sem demora. O início da data a partir da qual está prevista uma ação é chamado de *tempo nominal*. Por exemplo, para processar os dados para 1 de janeiro de 2017 o coordenador irá agendar a ação com um tempo nominal de 2017-01-01T00:00:00 GMT.

* Ponto 2: Dentro do intervalo de datas do fluxo de trabalho, o `dataset` elemento especifica onde procurar em HDFS os dados para uma determinada gama de datas, e configura como o Oozie determina se os dados ainda estão disponíveis para processamento.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    O caminho para os dados em HDFS é construído dinamicamente de acordo com a expressão fornecida no `uri-template` elemento. Neste coordenador, uma frequência de um dia também é usada com o conjunto de dados. Enquanto as datas de início e fim do controlo do elemento coordenador quando as ações são programadas (e definem os seus tempos nominais), o `initial-instance` conjunto `frequency` de dados controla o cálculo da data utilizada na construção do `uri-template` . Neste caso, desemagure a instância inicial para um dia antes do início do coordenador para garantir que recolhe os dados do primeiro dia (1/1/2017). O cálculo da data do conjunto de dados avança a partir do valor de `initial-instance` (12/31/2016) avançando em incrementos da frequência do conjunto de dados (um dia) até encontrar a data mais recente que não passa o tempo nominal definido pelo coordenador (2017-01-01T00:00:00 GMT para a primeira ação).

    O elemento vazio `done-flag` indica que quando Oozie verifica a presença de dados de entrada no momento indicado, Oozie determina os dados disponíveis pela presença de um diretório ou ficheiro. Neste caso, é a presença de um ficheiro CSV. Se estiver presente um ficheiro CSV, o Oozie assume que os dados estão prontos e lança uma instância de fluxo de trabalho para processar o ficheiro. Se não houver nenhum ficheiro CSV presente, Oozie assume que os dados ainda não estão prontos e que a execução do fluxo de trabalho entra em estado de espera.

* Ponto 3: O `data-in` elemento especifica a marca de tempos específica a utilizar como o tempo nominal para substituir os valores para o conjunto de `uri-template` dados associado.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Neste caso, deu o exemplo à expressão `${coord:current(0)}` , que se traduz na utilização do tempo nominal da ação, tal como inicialmente previsto pelo coordenador. Ou seja, quando o coordenador agenda a ação a correr com um tempo nominal de 01/01/2017, então 01/01/2017 é o que é usado para substituir as variáveis ANO (2017) e MÊS (01) no modelo URI. Uma vez que o modelo URI é calculado para este caso, Oozie verifica se o diretório ou ficheiro esperado está disponível e agenda a próxima execução do fluxo de trabalho em conformidade.

Os três pontos anteriores combinam-se para produzir uma situação em que o coordenador agenda o processamento dos dados de origem de forma diária.

* Ponto 1: O coordenador começa com uma data nominal de 2017-01-01.

* Ponto 2: Oozie procura dados disponíveis em `sourceDataFolder/2017-01-FlightData.csv` .

* Ponto 3: Quando Oozie encontra esse ficheiro, agenda uma instância do fluxo de trabalho que irá processar os dados para 2017-01-01. Oozie continua a processar para 2017-01-02. Esta avaliação repete-se até 2017-01-05.

Tal como acontece com os fluxos de trabalho, a configuração de um coordenador é definida num `job.properties` ficheiro, que tem um superconjunto das definições utilizadas pelo fluxo de trabalho.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

As únicas novas propriedades introduzidas neste `job.properties` ficheiro são:

| Propriedade | Fonte de valor |
| --- | --- |
| oozie.coord.application.path | Indica a localização do `coordinator.xml` ficheiro que contém o coordenador Oozie a executar. |
| hiveDailyTableNamePrefix | O prefixo utilizado ao criar dinamicamente o nome da mesa da mesa de preparação. |
| hiveDataFolderPrefix | O prefixo do caminho onde todas as mesas de preparação serão armazenadas. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Implementar e executar o Coordenador Oozie

Para executar o oleoduto com um coordenador, proceda de forma semelhante ao fluxo de trabalho, exceto que trabalhe a partir de uma pasta um nível acima da pasta que contenha o seu fluxo de trabalho. Esta convenção de pasta separa os coordenadores dos fluxos de trabalho no disco, para que possa associar um coordenador a diferentes fluxos de trabalho infantil.

1. Utilize o SCP da sua máquina local para copiar os ficheiros do coordenador até ao armazenamento local do nó de cabeça do seu cluster.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH no nó da cabeça.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Copie os ficheiros de coordenador para o HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Executar o coordenador.

    ```bash
    oozie job -config job.properties -run
    ```

5. Verifique o estado utilizando a Consola Web Oozie, desta vez selecionando o **separador Empregos coordenadores** e, em seguida,  **todos os trabalhos**.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png" alt-text="Empregos do Coordenador da Consola Web Oozie":::

6. Selecione uma instância de coordenador para mostrar a lista de ações agendadas. Neste caso, deverá ver quatro ações com tempos nominais na gama de 1/1/2017 a 1/4/2017.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png" alt-text="Trabalho de Coordenador de Consola Web Oozie":::

    Cada ação desta lista corresponde a uma instância do fluxo de trabalho que processa o valor de um dia de dados, onde o início desse dia é indicado pela hora nominal.

## <a name="next-steps"></a>Passos seguintes

[Documentação Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)
