---
title: Operacionalizar um pipeline de análise de dados - Azure
description: Configurar e executar um pipeline de dados de exemplo que é desencadeado por novos dados e produz resultados concisos.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 16c7af4d66bd550eb4a286de7c86c436b1fe10e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75922666"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacionalizar um pipeline de análise de dados

*Os gasodutos* de dados são poucas as soluções de análise de dados. Como o nome sugere, um pipeline de dados retira dados brutos, limpa e remodela-os conforme necessário, e depois normalmente realiza cálculos ou agregados antes de armazenar os dados processados. Os dados processados são consumidos por clientes, relatórios ou APIs. Um gasoduto de dados deve fornecer resultados repetíveis, seja num horário ou quando desencadeado por novos dados.

Este artigo descreve como operacionalizar os seus pipelines de dados para repetição, utilizando o Oozie em execução em clusters Hadoop HDInsight. O cenário de exemplo acompanha-o através de um pipeline de dados que prepara e processa dados da série de horário de voo das companhias aéreas.

No seguinte cenário, os dados de entrada são um ficheiro plano contendo um lote de dados de voo durante um mês. Estes dados de voo incluem informações como a origem e o aeroporto de destino, as milhas voadas, os horários de partida e chegada, e assim por diante. O objetivo com este oleoduto é resumir o desempenho diário da companhia aérea, onde cada companhia aérea tem uma linha para cada dia com os atrasos médios de partida e chegada em minutos, e o total de milhas voadas nesse dia.

| YEAR | MONTH | DAY_OF_MONTH | TRANSPORTADORA |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

O oleoduto de exemplo aguarda até chegar os dados de voo de um novo período de tempo, em seguida, armazena essa informação detalhada de voo no seu armazém de dados Apache Hive para análises a longo prazo. O oleoduto também cria um conjunto de dados muito menor que resume apenas os dados diários de voo. Estes dados diários de resumo do voo são enviados para uma Base de Dados SQL para fornecer relatórios, como para um website.

O diagrama que se segue ilustra o gasoduto exemplo.

![Visão geral do pipeline de dados do exemplo de voo hDI](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Visão geral da solução Apache Oozie

Este gasoduto utiliza Apache Oozie a funcionar num cluster Hadoop HDInsight.

A Oozie descreve os seus oleodutos em termos de *ações,* *fluxos*de trabalho e *coordenadores.* As ações determinam o trabalho real a realizar, como executar uma consulta da Colmeia. Os fluxos de trabalho definem a sequência de ações. Os coordenadores definem o calendário para quando o fluxo de trabalho é executado. Os coordenadores também podem aguardar a disponibilidade de novos dados antes de lançaruma instância do fluxo de trabalho.

O diagrama seguinte mostra o design de alto nível deste exemplo o oleoduto Oozie.

![Oozie Flight exemplo Data Pipeline](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Provision Recursos Azure

Este gasoduto requer uma Base de Dados Azure SQL e um aglomerado de Hadoop HDInsight no mesmo local. A Base de Dados Azure SQL armazena tanto os dados sumários produzidos pelo oleoduto como a Loja de Metadados Oozie.

### <a name="provision-azure-sql-database"></a>Base de Dados Provision Azure SQL

1. Crie uma base de dados Azure SQL. Consulte [criar uma base de dados Azure SQL no portal Azure](../sql-database/sql-database-single-database-get-started.md).

1. Para garantir que o seu cluster HDInsight pode aceder à base de dados Azure SQL conectada, configure as regras de firewall da Base de Dados Azure SQL para permitir que os serviços e recursos do Azure acedam ao servidor. Pode ativar esta opção no portal Azure selecionando a firewall do **servidor set**, e selecionando **os** serviços e recursos do **Enable Azure para aceder a este servidor** para o servidor ou base de dados Azure SQL. Para mais informações, consulte [Criar e gerir as regras de firewall IP](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Utilize o editor da [Consulta](../sql-database/sql-database-single-database-get-started.md#query-the-database) para executar as `dailyflights` seguintes declarações sQL para criar a tabela que armazenará os dados resumidos de cada execução do pipeline.

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

### <a name="provision-an-apache-hadoop-cluster"></a>Provisionum Aglomerado de Hadoop Apache

Crie um cluster Apache Hadoop com uma metaloja personalizada. Durante a criação do cluster a partir do portal, a partir do separador **Armazenamento,** certifique-se de selecionar a sua Base de Dados SQL em **definições de Metastore**. Para obter mais informações sobre a seleção de uma metaloja, consulte [Selecione uma metaloja personalizada durante](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)a criação do cluster . Para obter mais informações sobre a criação de clusters, consulte [Start Started com HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Verifique a instalação de túneis SSH

Para utilizar a Consola Web Oozie para visualizar o estado do seu coordenador e casos de fluxo de trabalho, instale um túnel SSH no seu cluster HDInsight. Para mais informações, consulte [o Túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Também pode utilizar o Chrome com a extensão [Foxy Proxy](https://getfoxyproxy.org/) para navegar nos recursos web do seu cluster através do túnel SSH. Configure-o para proxy todos `localhost` os pedidos através do hospedeiro no porto 9876 do túnel. Esta abordagem é compatível com o Subsistema Windows para O Linux, também conhecido como Bash no Windows 10.

1. Execute o seguinte comando para abrir um túnel SSH para o seu cluster, onde `CLUSTERNAME` está o nome do seu cluster:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verifique se o túnel está operacional navegando até Ambari no seu nó de cabeça navegando para:

    `http://headnodehost:8080`

1. Para aceder à **Consola Web Oozie** a partir de Ambari, navegue para **Oozie** > **Quick Links** > [servidor Ativo] > **Oozie Web UI**.

## <a name="configure-hive"></a>Configure colmeia

### <a name="upload-data"></a>Carregar dados

1. Descarregue um ficheiro CSV que contenha dados de voo durante um mês. Descarregue `2017-01-FlightData.zip` o seu ficheiro ZIP a partir do [repositório HDInsight GitHub](https://github.com/hdinsight/hdinsight-dev-guide) e desaperte-o para o ficheiro `2017-01-FlightData.csv`CSV .

1. Copie este ficheiro CSV até à conta de Armazenamento Azure anexada ao seu cluster HDInsight e coloque-o `/example/data/flights` na pasta.

    1. Utilize o SCP para copiar os ficheiros da sua máquina local para o armazenamento local do seu nó de cabeça de cluster HDInsight.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Edite o comando `CLUSTERNAME` abaixo substituindo pelo nome do seu cluster e, em seguida, introduza o comando:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. A partir da sua sessão ssh, use o comando HDFS para copiar o ficheiro do seu nó de cabeça armazenamento local para Armazenamento Azure.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Criar tabelas

Os dados da amostra já estão disponíveis. No entanto, o gasoduto requer duas tabelas da Colmeia`rawFlights`para processamento, uma para os`flights`dados de entrada ( ) e outra para os dados resumidos ( ). Crie estas tabelas em Ambari da seguinte forma.

1. Faça login em Ambari `http://headnodehost:8080`navegando para .

2. A partir da lista de serviços, selecione **Hive**.

    ![Lista de serviços Apache Ambari que seleciona a Colmeia](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Selecione **Ir ver** ao lado da etiqueta Hive View 2.0.

    ![Lista de resumo sumário da Colmeia Ambari Apache](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Na área de texto de consulta, colhe `rawFlights` as seguintes declarações para criar a tabela. A `rawFlights` tabela fornece um esquema de leitura para os ficheiros `/example/data/flights` CSV dentro da pasta em Armazenamento Azure.

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

5. Selecione **Executar** para criar a tabela.

    ![hdi ambari serviços hive consulta](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Para criar `flights` a tabela, substitua o texto na área de texto de consulta pelas seguintes declarações. A `flights` tabela é uma tabela gerida pela Hive que partilha dados carregados nele por ano, mês e dia do mês. Esta tabela conterá todos os dados históricos de voo, com a menor granularidade presente nos dados de origem de uma linha por voo.

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

7. Selecione **Executar** para criar a tabela.

## <a name="create-the-oozie-workflow"></a>Criar o fluxo de trabalho de Oozie

Os gasodutos normalmente processam dados em lotes por um determinado intervalo de tempo. Neste caso, o gasoduto processa os dados de voo diariamente. Esta abordagem permite que os ficheiros CSV de entrada cheguem diariamente, semanalmente, mensalmente ou anualmente.

O fluxo de trabalho da amostra processa os dados de voo dia-a-dia, em três passos principais:

1. Faça uma consulta da Hive para extrair os dados para a data desse dia, desde o ficheiro CSV de origem representado pela `rawFlights` tabela e insira os dados na `flights` tabela.
2. Execute uma consulta da Hive para criar dinamicamente uma mesa de preparação na Colmeia para o dia, que contém uma cópia dos dados de voo resumidos de dia e transportadora.
3. Utilize o Apache Sqoop para copiar todos os dados da `dailyflights` tabela diária de preparação na Colmeia para a tabela de destino na Base de Dados Azure SQL. Sqoop lê as linhas de origem a partir dos dados por trás da tabela Hive residente no Armazenamento Azure e coloca-as na Base de Dados SQL usando uma ligação JDBC.

Estes três passos são coordenados por um fluxo de trabalho de Oozie.

1. A partir da sua estação `job.properties`de trabalho local, crie um ficheiro chamado . Utilize o texto abaixo como conteúdo inicial para o ficheiro.
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
    | nomeNódeo | O caminho completo para o recipiente de armazenamento Azure ligado ao seu cluster HDInsight. |
    | jobTracker | O nome de anfitrião interno do nó de cabeça yARN do seu cluster ativo. Na página inicial de Ambari, selecione YARN da lista de serviços e, em seguida, escolha Ative Resource Manager. O nome de anfitrião URI é apresentado no topo da página. Apêndice do porto 8050. |
    | queueName | O nome da fila YARN usada ao agendar as ações da Colmeia. Deixe como padrão. |
    | oozie.use.system.libpath | Deixe como verdadeiro. |
    | appBase | O caminho para a subpasta em Armazenamento Azure onde implementa o fluxo de trabalho oozie e ficheiros de suporte. |
    | oozie.wf.application.path | A localização do fluxo `workflow.xml` de trabalho de Oozie para correr. |
    | hiveScriptLoadPartition | O caminho em Azure Storage para `hive-load-flights-partition.hql`o arquivo de consulta da Colmeia. |
    | hiveScriptCreateDailyTable | O caminho em Azure Storage para `hive-create-daily-summary-table.hql`o arquivo de consulta da Colmeia. |
    | hiveDailyTableName | O nome gerado dinamicamente para usar para a mesa de preparação. |
    | hiveDataFolder | O caminho em Armazenamento Azure para os dados contidos pela mesa de preparação. |
    | sqlDatabaseConnectionString | A cadeia de ligação sintaxe JDBC à sua Base de Dados Azure SQL. |
    | sqlDatabaseTableName | O nome da tabela na Base de Dados Azure SQL na qual as linhas sumárias são inseridas. Deixe `dailyflights`como. |
    | ano | A componente do ano do dia para o qual os resumos de voo são calculados. Deixe como está. |
    | month | A componente mensal do dia para o qual os resumos do voo são calculados. Deixe como está. |
    | day | A componente do dia do mês do dia para o qual os resumos do voo são calculados. Deixe como está. |

1. A partir da sua estação `hive-load-flights-partition.hql`de trabalho local, crie um ficheiro chamado . Utilize o código abaixo como conteúdo para o ficheiro.

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

    As variáveis Oozie usam `${variableName}`a sintaxe. Estas variáveis estão `job.properties` definidas no ficheiro. Oozie substitui os valores reais no tempo de execução.

1. A partir da sua estação `hive-create-daily-summary-table.hql`de trabalho local, crie um ficheiro chamado . Utilize o código abaixo como conteúdo para o ficheiro.

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
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Esta consulta cria uma tabela de encenação que armazenará apenas os dados resumidos por um dia, tome nota da declaração SELECT que calcula os atrasos médios e o total de distância voada por transportadora durante o dia. Os dados inseridos nesta tabela armazenados num local conhecido (o caminho indicado pela variável hiveDataFolder) para que possa ser usado como fonte de Sqoop no passo seguinte.

1. A partir da sua estação `workflow.xml`de trabalho local, crie um ficheiro chamado . Utilize o código abaixo como conteúdo para o ficheiro. Estes passos acima são expressos como ações separadas no ficheiro de fluxo de trabalho oozie.

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

As duas consultas da Hive são acedidas pelo seu caminho no Armazenamento `job.properties` Azure, e os restantes valores variáveis são fornecidos pelo ficheiro. Este ficheiro configura o fluxo de trabalho a decorrer para a data de 3 de janeiro de 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>Implementar e executar o fluxo de trabalho oozie

Utilize o SCP da sua sessão de`workflow.xml`bash para implementar o`hive-load-flights-partition.hql` `hive-create-daily-summary-table.hql`seu fluxo de`job.properties`trabalho Oozie (), as consultas da Colmeia ( e), e a configuração do trabalho ( ).  Em Oozie, `job.properties` só o ficheiro pode existir no armazenamento local do nódoado. Todos os outros ficheiros devem ser armazenados em HDFS, neste caso o Armazenamento Azure. A ação Sqoop utilizada pelo fluxo de trabalho depende de um controlador JDBC para comunicar com a sua Base de Dados SQL, que deve ser copiada do nó da cabeça para hDFS.

1. Crie `load_flights_by_day` a subpasta por baixo do caminho do utilizador no armazenamento local do nó de cabeça. A partir da sua sessão aberta, execute o seguinte comando:

    ```bash
    mkdir load_flights_by_day
    ```

1. Copie todos os ficheiros `workflow.xml` do `job.properties` diretório atual `load_flights_by_day` (o e os ficheiros) até à subpasta. A partir da sua estação de trabalho local, execute o seguinte comando:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Copiar ficheiros de fluxo de trabalho para HDFS. A partir da sua sessão aberta, execute os seguintes comandos:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Copiar `mssql-jdbc-7.0.0.jre8.jar` do nó da cabeça local para a pasta de fluxo de trabalho em HDFS. Reveja o comando conforme necessário se o seu cluster contiver um ficheiro de frasco diferente. Reveja `workflow.xml` como necessário para refletir um ficheiro de frascodiferente. A partir da sua sessão aberta, execute o seguinte comando:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Executar o fluxo de trabalho. A partir da sua sessão aberta, execute o seguinte comando:

    ```bash
    oozie job -config job.properties -run
    ```

1. Observe o estado utilizando a Consola Web Oozie. A partir de Ambari, selecione **Oozie,** **Quick Links,** e depois **Oozie Web Console**. Sob o separador **Workflow Jobs,** selecione **All Jobs**.

    ![hdi oozie web consola fluxos de trabalho](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Quando o estado for bem sucedido, consulte a tabela De base de dados SQL para visualizar as linhas inseridas. Utilizando o portal Azure, navegue até ao painel para a sua Base de Dados SQL, selecione **Ferramentas,** e abra o Editor de **Consulta**.

        SELECT * FROM dailyflights

Agora que o fluxo de trabalho está a decorrer para o único dia de teste, pode embrulhar este fluxo de trabalho com um coordenador que programa o fluxo de trabalho para que funcione diariamente.

## <a name="run-the-workflow-with-a-coordinator"></a>Executar o fluxo de trabalho com um coordenador

Para agendar este fluxo de trabalho para que funcione diariamente (ou todos os dias em data), pode utilizar um coordenador. Um coordenador é definido por um `coordinator.xml`ficheiro XML, por exemplo:

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

Como pode ver, a maioria do coordenador está apenas a passar informações de configuração para a instância de fluxo de trabalho. No entanto, há alguns itens importantes para chamar.

* Ponto 1: `start` `end` Os atributos e atributos no `coordinator-app` próprio elemento controlam o intervalo de tempo sobre o qual o coordenador corre.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Um coordenador é responsável por `start` agendar ações dentro do intervalo `end` e `frequency` data, de acordo com o intervalo especificado pelo atributo. Cada ação programada, por sua vez, executa o fluxo de trabalho conforme configurado. Na definição de coordenador acima, o coordenador está configurado para executar ações de 1 de janeiro de 2017 a 5 de janeiro de 2017. A frequência é definida para um dia pela expressão `${coord:days(1)}`de frequência da linguagem de expressão [Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) . Isto resulta na agendação do coordenador de uma ação (e, portanto, do fluxo de trabalho) uma vez por dia. Para intervalos de datas que estão no passado, como neste exemplo, a ação será programada para ser executada sem demora. O início da data a partir da qual está prevista uma ação chama-se *tempo nominal*. Por exemplo, para processar os dados para 1 de janeiro de 2017, o coordenador irá agendar uma ação com um tempo nominal de 2017-01-01T00:00:00 GMT.

* Ponto 2: Dentro do intervalo de `dataset` data do fluxo de trabalho, o elemento especifica onde procurar no HDFS os dados para uma determinada gama de datas, e explica como a Oozie determina se os dados ainda estão disponíveis para processamento.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    O caminho para os dados em HDFS é construído `uri-template` dinamicamente de acordo com a expressão fornecida no elemento. Neste coordenador, uma frequência de um dia também é usada com o conjunto de dados. Enquanto as datas de início e fim do controlo do elemento coordenador `initial-instance` quando `frequency` as ações estão programadas (e define os `uri-template`seus tempos nominais), o e no conjunto de dados controlam o cálculo da data utilizada na construção do . Neste caso, estabeleça a instância inicial para um dia antes do início do coordenador para garantir que recolhe os dados do primeiro dia (1/1/2017). O cálculo da data do conjunto de `initial-instance` dados avança a partir do valor de (12/31/2016) avançando em incrementos de frequência de conjunto de dados (um dia) até encontrar a data mais recente que não passa o tempo nominal definido pelo coordenador (2017-01-01T00:00:00 GMT para a primeira ação).

    O `done-flag` elemento vazio indica que quando a Oozie verifica a presença de dados de entrada no momento indicado, a Oozie determina os dados disponíveis pela presença de um diretório ou ficheiro. Neste caso, é a presença de um ficheiro CSV. Se um ficheiro csv estiver presente, a Oozie assume que os dados estão prontos e lança uma instância de fluxo de trabalho para processar o ficheiro. Se não houver nenhum ficheiro CSV presente, Oozie assume que os dados ainda não estão prontos e que a execução do fluxo de trabalho vai para um estado de espera.

* Ponto 3: `data-in` O elemento especifica a marca de tempo específica para `uri-template` usar como o tempo nominal ao substituir os valores para o conjunto de dados associado.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Neste caso, definir a instância `${coord:current(0)}`para a expressão , que se traduz na utilização do tempo nominal da ação, tal como inicialmente previsto pelo coordenador. Ou seja, quando o coordenador agenda a ação para correr com um tempo nominal de 01/01/2017, então 01/01/2017 é o que é usado para substituir as variáveis ANO (2017) e MÊS (01) no modelo URI. Uma vez que o modelo URI é calculado para este caso, Oozie verifica se o diretório ou arquivo esperado está disponível e marca a próxima execução do fluxo de trabalho em conformidade.

Os três pontos anteriores combinam-se para produzir uma situação em que o coordenador agenda o processamento dos dados de origem de forma diária.

* Ponto 1: O coordenador começa com uma data nominal de 2017-01-01.

* Ponto 2: Oozie procura `sourceDataFolder/2017-01-FlightData.csv`dados disponíveis em .

* Ponto 3: Quando o Oozie encontra esse ficheiro, ele marca uma instância do fluxo de trabalho que irá processar os dados para 2017-01-01. Oozie continua a processar para 2017-01-02. Esta avaliação repete-se até 2017-01-05.

Tal como acontece com os fluxos de `job.properties` trabalho, a configuração de um coordenador é definida num ficheiro, que tem um sobreconjunto das definições utilizadas pelo fluxo de trabalho.

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

As únicas novas propriedades `job.properties` introduzidas neste ficheiro são:

| Propriedade | Fonte de valor |
| --- | --- |
| oozie.coord.application.path | Indica a localização `coordinator.xml` do ficheiro que contém o coordenador oozie para executar. |
| hiveDailyTableNamePrefix | O prefixo utilizado ao criar dinamicamente o nome da mesa da mesa de preparação. |
| hiveDataFolderPrefix | O prefixo do caminho onde todas as mesas de preparação serão armazenadas. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Implementar e executar o Coordenador Oozie

Para executar o pipeline com um coordenador, proceda de forma semelhante à do fluxo de trabalho, exceto que trabalha a partir de uma pasta um nível acima da pasta que contém o seu fluxo de trabalho. Esta convenção de pastas separa os coordenadores dos fluxos de trabalho no disco, para que possa associar um coordenador a diferentes fluxos de trabalho infantil.

1. Utilize sCP da sua máquina local para copiar os ficheiros do coordenador até ao armazenamento local do nó de cabeça do seu cluster.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH no seu nó de cabeça.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Copie os ficheiros do coordenador para o HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Dirija o coordenador.

    ```bash
    oozie job -config job.properties -run
    ```

5. Verifique o estado utilizando a Consola Web Oozie, desta vez selecionando o separador **Empregos Coordenador,** e, em seguida, **Todos os trabalhos**.

    ![Oozie Web Console Coordenador Jobs](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Selecione uma instância de coordenador para apresentar a lista de ações programadas. Neste caso, deverá ver quatro ações com tempos nominais na ordem dos dias 1/1/2017 a 1/4/2017.

    ![Oozie Web Console Coordenador Job](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Cada ação desta lista corresponde a uma instância do fluxo de trabalho que processa um dia de dados, onde o início desse dia é indicado pelo tempo nominal.

## <a name="next-steps"></a>Passos seguintes

[Documentação Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)
