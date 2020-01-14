---
title: Operacionalize um pipeline de análise de dados – Azure
description: Configure e execute um pipeline de dados de exemplo que é disparado por novos dados e produz resultados concisos.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 16c7af4d66bd550eb4a286de7c86c436b1fe10e2
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922666"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacionalizar um pipeline de análise de dados

*Pipelines de dados* em muitas soluções de análise de dados. Como o nome sugere, um pipeline de dados usa dados brutos, limpa e remodela-os conforme necessário e, em seguida, normalmente executa cálculos ou agregações antes de armazenar os dados processados. Os dados processados são consumidos por clientes, relatórios ou APIs. Um pipeline de dados deve fornecer resultados repetíveis, seja em um agendamento ou quando disparado por novos dados.

Este artigo descreve como colocar em operação os pipelines de dados para repetição, usando Oozie em execução em clusters Hadoop do HDInsight. O cenário de exemplo orienta você por um pipeline de dados que prepara e processa dados de série temporal de voo aérea.

No cenário a seguir, os dados de entrada são um arquivo simples que contém um lote de dados de voo para um mês. Esses dados de vôo incluem informações como o aeroporto de origem e de destino, as milhas fluem, os tempos de partida e de chegada e assim por diante. O objetivo desse pipeline é resumir o desempenho diário da companhia aérea, em que cada companhia aérea tem uma linha para cada dia com os atrasos médios de partida e chegada em minutos e o total de milhas fluir nesse dia.

| YEAR | MONTH | DAY_OF_MONTH | DA |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1 | 3 | DL | 6,935409 | -2.1893024 | 1909696 |

O pipeline de exemplo aguarda até que os dados de vôo de um novo período de tempo cheguem e, em seguida, armazena essas informações detalhadas de vôo em seu Apache Hive data warehouse para análises de longo prazo. O pipeline também cria um conjunto de dados muito menor que resume apenas os data de vôo diário. Esses dados de Resumo de vôo diário são enviados para um banco de dado SQL para fornecer relatórios, como para um site.

O diagrama a seguir ilustra o pipeline de exemplo.

![Visão geral do pipeline de dados de exemplo de vôo HDI](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Visão geral da solução Apache Oozie

Esse pipeline usa o Apache Oozie em execução em um cluster Hadoop do HDInsight.

Oozie descreve seus pipelines em termos de *ações*, *fluxos de trabalho*e *coordenadores*. As ações determinam o trabalho real a ser executado, como a execução de uma consulta de Hive. Os fluxos de trabalho definem a sequência de ações. Os coordenadores definem o agendamento para quando o fluxo de trabalho é executado. Os coordenadores também podem aguardar a disponibilidade de novos dados antes de iniciar uma instância do fluxo de trabalho.

O diagrama a seguir mostra o design de alto nível deste pipeline de exemplo do Oozie.

![Pipeline de dados de exemplo de vôo Oozie](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Provisionar recursos do Azure

Esse pipeline requer um banco de dados SQL do Azure e um cluster HDInsight Hadoop no mesmo local. O banco de dados SQL do Azure armazena tanto o dado de resumo produzido pelo pipeline quanto o repositório de metadados Oozie.

### <a name="provision-azure-sql-database"></a>Provisionar banco de dados SQL do Azure

1. Crie um banco de dados SQL do Azure. Consulte [criar um banco de dados SQL do Azure no portal do Azure](../sql-database/sql-database-single-database-get-started.md).

1. Para garantir que seu cluster HDInsight possa acessar o banco de dados SQL do Azure conectado, configure as regras de firewall do banco de dados SQL do Azure para permitir que os serviços e recursos do Azure acessem o servidor. Você pode habilitar essa opção no portal do Azure selecionando **definir o Firewall do servidor**e selecionando **ativado em** **permitir que os serviços e recursos do Azure acessem este servidor** para o banco de dados ou servidor de banco de dados SQL do Azure. Para obter mais informações, consulte [criar e gerenciar regras de firewall de IP](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Use o [Editor de consultas](../sql-database/sql-database-single-database-get-started.md#query-the-database) para executar as seguintes instruções SQL para criar a tabela de `dailyflights` que armazenará os dados resumidos de cada execução do pipeline.

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

O banco de dados SQL do Azure agora está pronto.

### <a name="provision-an-apache-hadoop-cluster"></a>Provisionar um cluster Apache Hadoop

Crie um cluster Apache Hadoop com um metastore personalizado. Durante a criação do cluster no portal, na guia **armazenamento** , verifique se você selecionou o banco de dados SQL em **configurações do metastore**. Para obter mais informações sobre como selecionar um metastore, consulte [selecionar um metastore personalizado durante a criação do cluster](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Para obter mais informações sobre a criação de cluster, consulte Introdução [ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Verificar a configuração do túnel SSH

Para usar o console Web do Oozie para exibir o status de suas instâncias de coordenador e fluxo de trabalho, configure um túnel SSH para seu cluster HDInsight. Para obter mais informações, consulte [SSH Tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Você também pode usar o Chrome com a extensão de [proxy Foxy](https://getfoxyproxy.org/) para procurar os recursos da Web do cluster no túnel SSH. Configure-o para fazer o proxy de todas as solicitações por meio do host `localhost` na porta do túnel 9876. Essa abordagem é compatível com o subsistema do Windows para Linux, também conhecido como bash no Windows 10.

1. Execute o comando a seguir para abrir um túnel SSH para o cluster, em que `CLUSTERNAME` é o nome do cluster:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verifique se o túnel está operacional navegando até Ambari em seu nó de cabeçalho navegando até:

    `http://headnodehost:8080`

1. Para acessar o **console Web do Oozie** de dentro de Ambari, navegue até **Oozie** > **links rápidos** > [Active Server] > **interface do usuário da Web do Oozie**.

## <a name="configure-hive"></a>Configurar o hive

### <a name="upload-data"></a>Carregar dados

1. Baixe um arquivo CSV de exemplo que contém dados de voo para um mês. Baixe seu arquivo ZIP `2017-01-FlightData.zip` do [repositório GitHub do HDInsight](https://github.com/hdinsight/hdinsight-dev-guide) e descompacte-o no arquivo CSV `2017-01-FlightData.csv`.

1. Copie esse arquivo CSV até a conta de armazenamento do Azure anexada ao seu cluster HDInsight e coloque-o na pasta `/example/data/flights`.

    1. Use o SCP para copiar os arquivos do computador local para o armazenamento local do nó principal do cluster HDInsight.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Use o [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir, substituindo `CLUSTERNAME` pelo nome do cluster e, em seguida, digite o comando:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. A partir da sessão SSH, use o comando HDFS para copiar o arquivo do armazenamento local do nó de cabeçalho para o armazenamento do Azure.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Criar tabelas

Os dados de exemplo agora estão disponíveis. No entanto, o pipeline requer duas tabelas Hive para processamento, uma para os dados de entrada (`rawFlights`) e outra para os dados resumidos (`flights`). Crie essas tabelas no Ambari da seguinte maneira.

1. Faça logon no Ambari navegando até `http://headnodehost:8080`.

2. Na lista de serviços, selecione **Hive**.

    ![Lista de serviços Apache Ambari selecionando o hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Selecione **ir para exibição** ao lado do rótulo de exibição do hive 2,0.

    ![Lista de Resumo de Apache Hive de Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Na área de texto consulta, Cole as instruções a seguir para criar a tabela `rawFlights`. A tabela `rawFlights` fornece um esquema em leitura para os arquivos CSV dentro da pasta `/example/data/flights` no armazenamento do Azure.

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

5. Selecione **executar** para criar a tabela.

    ![consulta de hive de serviços HDI ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Para criar a tabela de `flights`, substitua o texto na área de texto da consulta pelas instruções a seguir. A tabela `flights` é uma tabela gerenciada por Hive que particiona os dados carregados nela por ano, mês e dia do mês. Esta tabela conterá todos os dados de vôo históricos, com a granularidade mais baixa presente nos dados de origem de uma linha por voo.

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

7. Selecione **executar** para criar a tabela.

## <a name="create-the-oozie-workflow"></a>Criar o fluxo de trabalho do Oozie

Os pipelines normalmente processam dados em lotes por um determinado intervalo de tempo. Nesse caso, o pipeline processa os dados de vôo diariamente. Essa abordagem permite que os arquivos CSV de entrada cheguem diariamente, semanalmente, mensalmente ou anualmente.

O fluxo de trabalho de exemplo processa os dados de vôo dia a dia, em três etapas principais:

1. Execute uma consulta de Hive para extrair os dados do intervalo de datas desse dia do arquivo CSV de origem representado pela tabela `rawFlights` e inserir os dados na tabela `flights`.
2. Execute uma consulta de Hive para criar dinamicamente uma tabela de preparo no hive para o dia, que contém uma cópia dos dados de vôo resumidos por dia e pela operadora.
3. Use o Apache Sqoop para copiar todos os dados da tabela de preparo diária no hive para a tabela de `dailyflights` de destino no banco de dados SQL do Azure. O Sqoop lê as linhas de origem dos dados por trás da tabela do hive que reside no armazenamento do Azure e carrega-as no SQL Database usando uma conexão JDBC.

Essas três etapas são coordenadas por um fluxo de trabalho do Oozie.

1. Na estação de trabalho local, crie um arquivo chamado `job.properties`. Use o texto abaixo como o conteúdo inicial para o arquivo.
Em seguida, atualize os valores para seu ambiente específico. A tabela abaixo do texto resume cada uma das propriedades e indica onde você pode encontrar os valores para seu próprio ambiente.

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

    | Propriedade | Origem do valor |
    | --- | --- |
    | nameNode | O caminho completo para o contêiner de armazenamento do Azure anexado ao seu cluster HDInsight. |
    | jobTracker | O nome do host interno para o nó de cabeçalho do YARN do seu cluster ativo. Na home page Ambari, selecione YARN na lista de serviços e escolha active Resource Manager. O URI do nome do host é exibido na parte superior da página. Acrescente a porta 8050. |
    | queueName | O nome da fila YARN usada ao agendar as ações do hive. Deixe como padrão. |
    | oozie.use.system.libpath | Deixe como verdadeiro. |
    | appBase | O caminho para a subpasta no armazenamento do Azure em que você implanta o fluxo de trabalho do Oozie e os arquivos de suporte. |
    | oozie.wf.application.path | O local do fluxo de trabalho Oozie `workflow.xml` a ser executado. |
    | hiveScriptLoadPartition | O caminho no armazenamento do Azure para o arquivo de consulta do hive `hive-load-flights-partition.hql`. |
    | hiveScriptCreateDailyTable | O caminho no armazenamento do Azure para o arquivo de consulta do hive `hive-create-daily-summary-table.hql`. |
    | hiveDailyTableName | O nome gerado dinamicamente a ser usado para a tabela de preparo. |
    | hiveDataFolder | O caminho no armazenamento do Azure para os dados contidos na tabela de preparo. |
    | sqlDatabaseConnectionString | A cadeia de conexão de sintaxe JDBC para o banco de dados SQL do Azure. |
    | sqlDatabaseTableName | O nome da tabela no banco de dados SQL do Azure no qual as linhas de resumo são inseridas. Deixe como `dailyflights`. |
    | ano | O componente de ano do dia para o qual os resumos de voo são computados. Deixe como está. |
    | mês | O componente de mês do dia para o qual os resumos de voo são computados. Deixe como está. |
    | dia | O componente de dia do mês do dia para o qual os resumos de voo são computados. Deixe como está. |

1. Na estação de trabalho local, crie um arquivo chamado `hive-load-flights-partition.hql`. Use o código abaixo como o conteúdo para o arquivo.

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

    As variáveis Oozie usam a sintaxe `${variableName}`. Essas variáveis são definidas no arquivo `job.properties`. Oozie substitui os valores reais em tempo de execução.

1. Na estação de trabalho local, crie um arquivo chamado `hive-create-daily-summary-table.hql`. Use o código abaixo como o conteúdo para o arquivo.

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

    Essa consulta cria uma tabela de preparo que armazenará apenas os dados resumidos de um dia, anote a instrução SELECT que computa os atrasos médios e o total de distância da portadora por dia. Os dados inseridos nessa tabela armazenados em um local conhecido (o caminho indicado pela variável hiveDataFolder) para que ele possa ser usado como a origem para Sqoop na próxima etapa.

1. Na estação de trabalho local, crie um arquivo chamado `workflow.xml`. Use o código abaixo como o conteúdo para o arquivo. Essas etapas acima são expressas como ações separadas no arquivo de fluxo de trabalho Oozie.

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

As duas consultas de Hive são acessadas por seu caminho no armazenamento do Azure e os valores de variáveis restantes são fornecidos pelo arquivo de `job.properties`. Este arquivo configura o fluxo de trabalho para ser executado na data 3 de janeiro de 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>Implantar e executar o fluxo de trabalho do Oozie

Use o SCP de sua sessão de bash para implantar seu Oozie Workflow (`workflow.xml`), as consultas de Hive (`hive-load-flights-partition.hql` e `hive-create-daily-summary-table.hql`) e a configuração do trabalho (`job.properties`).  No Oozie, somente o arquivo `job.properties` pode existir no armazenamento local do cabeçalho. Todos os outros arquivos devem ser armazenados no HDFS, nesse caso, o armazenamento do Azure. A ação Sqoop usada pelo fluxo de trabalho depende de um driver JDBC para se comunicar com o banco de dados SQL, que deve ser copiado do nó principal para o HDFS.

1. Crie a subpasta `load_flights_by_day` sob o caminho do usuário no armazenamento local do nó principal. Em sua sessão SSH aberta, execute o seguinte comando:

    ```bash
    mkdir load_flights_by_day
    ```

1. Copie todos os arquivos no diretório atual (os arquivos `workflow.xml` e `job.properties`) até a subpasta `load_flights_by_day`. Na estação de trabalho local, execute o seguinte comando:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Copiar arquivos de fluxo de trabalho para HDFS. Em sua sessão SSH aberta, execute os seguintes comandos:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Copie `mssql-jdbc-7.0.0.jre8.jar` do nó de cabeçalho local para a pasta de fluxo de trabalho no HDFS. Revisar o comando conforme necessário se o cluster contiver um arquivo JAR diferente. Revisar `workflow.xml` conforme necessário para refletir um arquivo JAR diferente. Em sua sessão SSH aberta, execute o seguinte comando:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Execute o fluxo de trabalho. Em sua sessão SSH aberta, execute o seguinte comando:

    ```bash
    oozie job -config job.properties -run
    ```

1. Observe o status usando o console Web do Oozie. Em Ambari, selecione **Oozie**, **links rápidos**e, em seguida, **console Web do Oozie**. Na guia **trabalhos de fluxo** de trabalho, selecione **todos os trabalhos**.

    ![fluxos de trabalho do console Web do HDI oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Quando o status for bem-sucedido, consulte a tabela do banco de dados SQL para exibir as linhas inseridas. Usando o portal do Azure, navegue até o painel do banco de dados SQL, selecione **ferramentas**e abra o **Editor de consultas**.

        SELECT * FROM dailyflights

Agora que o fluxo de trabalho está em execução para o dia de teste único, você pode encapsular esse fluxo de trabalho com um coordenador que agenda o fluxo de trabalho para que ele seja executado diariamente.

## <a name="run-the-workflow-with-a-coordinator"></a>Executar o fluxo de trabalho com um coordenador

Para agendar esse fluxo de trabalho para que ele seja executado diariamente (ou todos os dias em um intervalo de datas), você pode usar um coordenador. Um coordenador é definido por um arquivo XML, por exemplo `coordinator.xml`:

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

Como você pode ver, a maior parte do coordenador está apenas passando informações de configuração para a instância de fluxo de trabalho. No entanto, há alguns itens importantes para chamar.

* Ponto 1: os atributos `start` e `end` no próprio elemento `coordinator-app` controlam o intervalo de tempo durante o qual o coordenador é executado.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Um coordenador é responsável por agendar ações dentro do `start` e `end` intervalo de datas, de acordo com o intervalo especificado pelo atributo `frequency`. Cada ação agendada, por sua vez, executa o fluxo de trabalho conforme configurado. Na definição de coordenador acima, o coordenador está configurado para executar ações de 1º de janeiro de 2017 a 5 de janeiro de 2017. A frequência é definida como um dia pela expressão de frequência da [linguagem Oozie expression](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) `${coord:days(1)}`. Isso resulta no agendamento de um coordenador de uma ação (e, portanto, do fluxo de trabalho) uma vez por dia. Para intervalos de datas que estão no passado, como neste exemplo, a ação será agendada para ser executada sem atraso. O início da data a partir da qual uma ação está agendada para execução é chamada de *tempo nominal*. Por exemplo, para processar os dados de 1º de janeiro de 2017, o coordenador agendará a ação com um tempo nominal de 2017-01-01T00:00:00 GMT.

* Ponto 2: dentro do intervalo de datas do fluxo de trabalho, o elemento `dataset` especifica onde procurar no HDFS para os dados de um determinado intervalo de datas e configura como o Oozie determina se os dados ainda estão disponíveis para processamento.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    O caminho para os dados no HDFS é compilado dinamicamente de acordo com a expressão fornecida no elemento `uri-template`. Nesse coordenador, uma frequência de um dia também é usada com o conjunto de um. Enquanto as datas de início e de término no elemento coordenador controlam quando as ações são agendadas (e definem suas horas nominais), o `initial-instance` e `frequency` no conjunto de dados controlam o cálculo da data usada na construção da `uri-template`. Nesse caso, defina a instância inicial como um dia antes do início do coordenador para garantir que ela pegue o valor de dados do primeiro dia (1/1/2017). O cálculo de data do conjunto de dados é encaminhado do valor de `initial-instance` (12/31/2016) avançando em incrementos de frequência de conjunto (um dia) até encontrar a data mais recente que não passa o tempo nominal definido pelo coordenador (2017-01-01T00:00:00 GMT para a primeira ação).

    O elemento `done-flag` vazio indica que quando o Oozie verifica a presença de dados de entrada na hora indicada, Oozie determina os dados se disponíveis por presença de um diretório ou arquivo. Nesse caso, é a presença de um arquivo CSV. Se um arquivo CSV estiver presente, o Oozie assumirá que os dados estão prontos e iniciará uma instância de fluxo de trabalho para processar o arquivo. Se não houver nenhum arquivo CSV presente, o Oozie assumirá que os dados ainda não estão prontos e que a execução do fluxo de trabalho entrará em um estado de espera.

* Ponto 3: o elemento `data-in` especifica o carimbo de data/hora específico a ser usado como o tempo nominal ao substituir os valores em `uri-template` para o conjunto de dado associado.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Nesse caso, defina a instância para a expressão `${coord:current(0)}`, que se traduz em usar o tempo nominal da ação como agendado originalmente pelo coordenador. Em outras palavras, quando o coordenador agenda a ação a ser executada com um tempo nominal de 01/01/2017, 01/01/2017 é o que é usado para substituir as variáveis YEAR (2017) e MONTH (01) no modelo de URI. Depois que o modelo de URI é computado para essa instância, Oozie verifica se o diretório ou arquivo esperado está disponível e agenda a próxima execução do fluxo de trabalho de acordo.

Os três pontos anteriores são combinados para gerar uma situação em que o coordenador agenda o processamento dos dados de origem diariamente.

* Ponto 1: o coordenador começa com uma data nominal de 2017-01-01.

* Ponto 2: o Oozie procura os dados disponíveis em `sourceDataFolder/2017-01-FlightData.csv`.

* Ponto 3: quando o Oozie encontra esse arquivo, ele agenda uma instância do fluxo de trabalho que processará os dados para 2017-01-01. Oozie, em seguida, continua o processamento para 2017-01-02. Essa avaliação repete até, mas não incluindo 2017-01-05.

Assim como acontece com os fluxos de trabalho, a configuração de um coordenador é definida em um arquivo `job.properties`, que tem um superconjunto das configurações usadas pelo fluxo de trabalho.

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

As únicas novas propriedades introduzidas neste `job.properties` arquivo são:

| Propriedade | Origem do valor |
| --- | --- |
| oozie.coord.application.path | Indica o local do arquivo de `coordinator.xml` que contém o coordenador Oozie a ser executado. |
| hiveDailyTableNamePrefix | O prefixo usado ao criar dinamicamente o nome da tabela de preparo. |
| hiveDataFolderPrefix | O prefixo do caminho onde todas as tabelas de preparo serão armazenadas. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Implantar e executar o coordenador de Oozie

Para executar o pipeline com um coordenador, prossiga de maneira semelhante para o fluxo de trabalho, exceto que você trabalha de uma pasta um nível acima da pasta que contém o fluxo de trabalho. Essa convenção de pasta separa os coordenadores dos fluxos de trabalho no disco, para que você possa associar um coordenador a diferentes fluxos de trabalho filho.

1. Use o SCP do computador local para copiar os arquivos do coordenador para o armazenamento local do nó principal do cluster.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH em seu nó de cabeçalho.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Copie os arquivos do coordenador para o HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Execute o coordenador.

    ```bash
    oozie job -config job.properties -run
    ```

5. Verifique o status usando o console Web do Oozie, desta vez selecionando a guia **trabalhos do coordenador** e, em seguida, **todos os trabalhos**.

    ![Trabalhos do coordenador do console Web do Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Selecione uma instância de coordenador para exibir a lista de ações agendadas. Nesse caso, você deve ver quatro ações com tempos nominais no intervalo de 1/1/2017 a 1/4/2017.

    ![Trabalho do coordenador do console Web do Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Cada ação nessa lista corresponde a uma instância do fluxo de trabalho que processa dados de um dia, em que o início desse dia é indicado pelo tempo nominal.

## <a name="next-steps"></a>Passos seguintes

[Documentação do Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)
