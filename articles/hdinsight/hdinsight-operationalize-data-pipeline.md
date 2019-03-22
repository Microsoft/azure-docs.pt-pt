---
title: Operacionalizar um pipeline de análise de dados - Azure
description: Configurar e executar um pipeline de dados de exemplo que é acionado por novos dados e produz resultados concisos.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: b5e7864eae56a6f066590191373d35cb57693f37
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311684"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacionalizar um pipeline de análise de dados

*Pipelines de dados* underly muitas soluções de análise de dados. Como o nome sugere, um pipeline de dados aceita dados não processados, limpa e reformulação-la conforme necessário e, em seguida, normalmente executa cálculos ou agregações antes de armazenar os dados processados. Os dados processados são consumidos por clientes, relatórios ou APIs. Um pipeline de dados tem de fornecer resultados repetíveis, se, com base numa agenda, ou quando acionada por novos dados.

Este artigo descreve como operacionalizar os seus pipelines de dados para a capacidade de repetição, utilizar o Oozie em execução em clusters do HDInsight Hadoop. O cenário de exemplo orienta-o através de um pipeline de dados que prepara e processa dados de séries temporais de vôo de companhia aérea.

No cenário seguinte, os dados de entrada são um arquivo simples que contém um lote de dados de voo durante um mês. Estes dados de voo incluem informações como o aeroporto de origem e destino, quilómetros voou, a mudança e tempos de chegada e assim por diante. O objetivo com este pipeline é resumir o desempenho de companhia aérea diário, onde cada companhia aérea tem uma linha para cada dia, com os atrasos de mudança e chegada média em minutos e os total quilómetros voou nesse dia.

| YEAR | MONTH | DAY_OF_MONTH | OPERADORA |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

O pipeline de exemplo aguarda até que os dados de um novo período de tempo de voo chegam, em seguida, armazena essas informações de voo detalhadas para o armazém de dados do Apache Hive para análises de longo prazo. O pipeline cria também um muito mais pequeno conjunto de dados que resume apenas os dados diários de voo. Estes dados de resumo de voo diária são enviados para uma base de dados SQL para fornecer relatórios, por exemplo, para um Web site.

O diagrama seguinte ilustra o pipeline de exemplo.

![Pipeline de dados de voo](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Descrição geral da solução do Apache Oozie

Este pipeline utiliza o Apache Oozie em execução num cluster do HDInsight Hadoop.

Oozie descreve os respetivos pipelines em termos de *ações*, *fluxos de trabalho*, e *coordenadores*. As ações determinam o trabalho real a ser executado, como a execução de uma consulta do Hive. Fluxos de trabalho definem a sequência de ações. Os coordenadores de definem a agenda para quando o fluxo de trabalho é executado. Os coordenadores também podem aguardar na disponibilidade de novos dados antes de iniciar uma instância do fluxo de trabalho.

O diagrama seguinte mostra o design de alto nível do pipeline de Oozie neste exemplo.

![Pipeline de dados de voo Oozie](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Aprovisionar recursos do Azure

Este pipeline requer uma base de dados do SQL do Azure e um cluster de Hadoop do HDInsight na mesma localização. A base de dados do SQL do Azure armazena tanto os dados de resumos do pipeline e o armazenamento de metadados do Oozie produzidos.

#### <a name="provision-azure-sql-database"></a>Base de dados SQL do Azure de aprovisionamento

1. Utilizar o portal do Azure, criar um novo grupo de recursos com o nome `oozie` para conter todos os recursos utilizados por este exemplo.
2. Dentro do `oozie` grupo de recursos, aprovisionar um servidor de SQL do Azure e base de dados. Não é necessário um banco de dados maior do que o escalão de preço S1 Standard.
3. Utilizar o portal do Azure, navegue para o painel da base de dados do SQL recentemente implementado e selecione **ferramentas**.

    ![Botão de ferramentas](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Selecione **Editor de consultas**.

    ![Botão do Editor de consultas](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. Na **Editor de consultas** painel, selecione **início de sessão**.

    ![Botão de início de sessão](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Introduza as credenciais da base de dados SQL e selecione **OK**.

   ![Formulário de início de sessão](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. Na área de texto do Editor de consultas, introduza as seguintes instruções SQL para criar o `dailyflights` tabela que irá armazenar os dados resumidos do cada execução do pipeline.

    ```
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

8. Selecione **executar** para executar as instruções SQL.

    ![Botão de execução](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

A base de dados do SQL do Azure está agora pronta.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Aprovisionar um Cluster de Hadoop do HDInsight

1. No portal do Azure, selecione **+ novo** e procure o HDInsight.
2. Selecione **Criar**.
3. No painel de noções básicas, forneça um nome exclusivo para o seu cluster e escolher a sua subscrição do Azure.

    ![Nome do cluster HDInsight e subscrição](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. Na **tipo de Cluster** painel, selecione a **Hadoop** tipo, de cluster **Linux** sistema operativo e a versão mais recente do HDInsight cluster. Deixe o **escalão do Cluster** na **padrão**.

    ![Tipo de cluster do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Escolher **selecione** para aplicar a sua seleção de tipo de cluster.
6. Concluir o **Noções básicas** painel ao fornecer uma palavra-passe de início de sessão e selecionar seu `oozie` recursos de grupo da lista, em seguida, selecione **seguinte**.

    ![Painel Noções básicas do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. Na **armazenamento** painel, deixe o armazenamento primário escreva definido como **armazenamento do Azure**, selecione **criar novo**e forneça um nome para a nova conta.

    ![Definições de conta de armazenamento do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. Para o **definições de Metastore**, em **Selecione uma base de dados do SQL para Hive**, selecione a base de dados que criou anteriormente.

    ![Definições de Metastore Hive do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Selecione **autenticar a base de dados SQL**.

    ![Metastore de Hive do HDInsight autenticar](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Introduza o seu nome de utilizador de base de dados SQL e a palavra-passe e escolha **selecione**. 

       ![Metastore Hive do HDInsight autenticar o início de sessão](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Volta a **definições de Metastore** painel, selecione a base de dados para os metadados do Oozie armazenar e autenticar-se tal como fez anteriormente. 

       ![Definições de Metastore do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Selecione **Seguinte**.
13. Sobre o **resumo** painel, selecione **criar** para implementar o seu cluster.

### <a name="verify-ssh-tunneling-setup"></a>Certifique-se a configuração de túnel SSH

Para utilizar a consola Web do Oozie para ver o estado das coordenador e das instâncias de fluxo de trabalho, configure um túnel SSH ao seu cluster do HDInsight. Para obter mais informações, consulte [túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Também pode utilizar o Chrome com o [Foxy Proxy](https://getfoxyproxy.org/) extensão para procurar recursos da web do seu cluster através do túnel SSH. Configurar a proxy de todos os pedidos através do anfitrião do `localhost` na porta do túnel 9876. Essa abordagem é compatível com o subsistema Windows para Linux, também conhecido como Bash no Windows 10.

1. Execute o seguinte comando para abrir um túnel SSH para o cluster:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Certifique-se de que o túnel está operacional ao navegar para Ambari no seu nó principal, ao navegar para:

    http:\//headnodehost:8080

3. Para aceder a **consola da Web de Oozie** na Ambari, selecione **Oozie**, **ligações rápidas**e, em seguida, selecione **consola da Web de Oozie**.

### <a name="configure-hive"></a>Configurar o Hive

1. Transferir um ficheiro CSV de exemplo que contém dados de voo durante um mês. Transfira o ficheiro ZIP `2017-01-FlightData.zip` partir do [repositório do GitHub do HDInsight](https://github.com/hdinsight/hdinsight-dev-guide) e deszipe-o para o ficheiro CSV `2017-01-FlightData.csv`. 

2. Copie este ficheiro CSV para a conta de armazenamento do Azure ligado ao seu cluster do HDInsight e colocá-la a `/example/data/flights` pasta.

Pode copiar o ficheiro com o SCP no seu `bash` sessão de shell.

1. Utilize o SCP para copiar os ficheiros do seu computador local para o armazenamento local do seu nó principal do cluster de HDInsight.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Utilize o comando do HDFS para copiar o ficheiro do seu armazenamento local do nó principal para armazenamento do Azure.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Os dados de exemplo já estão disponíveis. No entanto, o pipeline requer duas tabelas do Hive para processar, um para os dados de entrada (`rawFlights`) e outro para os dados resumidos (`flights`). Crie essas tabelas no Ambari da seguinte forma.

1. Inicie sessão no Ambari ao navegar para o http:\//headnodehost:8080.
2. Na lista de serviços, selecione **Hive**.

    ![Selecionar o ramo de registo no Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Selecione **vá para a exibição** junto à etiqueta do 2.0 de vista do Hive.

    ![Selecionar a vista do Hive no Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Na área de texto de consulta, cole as seguintes instruções para criar o `rawFlights` tabela. O `rawFlights` tabela fornece uma esquema na leitura de arquivos CSV dentro do `/example/data/flights` pasta no armazenamento do Azure. 

    ```
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

5. Selecione **Execute** para criar a tabela.

    ![Consulta do Hive no Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Para criar o `flights` de tabela, substitua o texto na área de texto de consulta com as seguintes instruções. O `flights` tabela é uma tabela de gerido de Hive que particiona os dados carregados para o mesmo por ano, mês e dia do mês. Esta tabela irá conter todos os dados históricos de voo, com a granularidade mais baixa presente nos dados de origem de uma linha por voo.

    ```
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

7. Selecione **Execute** para criar a tabela.

### <a name="create-the-oozie-workflow"></a>Criar o fluxo de trabalho do Oozie

Normalmente, de pipelines de processam dados em lotes por um intervalo de tempo especificado. Neste caso, o pipeline processa os dados de voo diariamente. Esta abordagem permite que os ficheiros CSV de entrada a chegada diariamente, semanalmente, mensalmente ou anualmente.

O fluxo de trabalho de exemplo processa o vôo dados dia para dia, em três passos principais:

1. Executar uma consulta do Hive para extrair os dados de intervalo de datas nesse dia do ficheiro CSV de origem representado pela `rawFlights` de tabela e inserir os dados para o `flights` tabela.
2. Execute uma consulta do Hive para criar dinamicamente uma tabela de teste no ramo de registo para o dia, que contém uma cópia dos dados de voo resumidos por dia e operadora.
3. Utilizar o Apache Sqoop para copiar todos os dados de tabela de teste diária no ramo de registo para o destino `dailyflights` tabela na base de dados do Azure SQL. Sqoop lê as linhas de origem de dados por trás da tabela do Hive que residem no armazenamento do Azure e carrega-os para a base de dados SQL através de uma ligação de JDBC.

Estes três passos são coordenados por um fluxo de trabalho do Oozie. 

1. Criar uma consulta no arquivo `hive-load-flights-partition.hql`.

    ```
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

    Variáveis de Oozie utilizam a sintaxe `${variableName}`. Estas variáveis são definidas `job.properties` de ficheiros, tal como descrito no passo subsequente. Oozie substitui os valores reais no tempo de execução.

2. Criar uma consulta no arquivo `hive-create-daily-summary-table.hql`.

    ```
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

    Esta consulta cria uma tabela de teste que irá armazenar apenas os dados resumidos por um dia, tome nota da instrução SELECT que computa a atrasos de média e total de distância voou pela operadora por dia. Os dados inseridos nessa tabela armazenada num local conhecido (o caminho indicado pela variável hiveDataFolder) para que ele pode ser utilizado como origem para Sqoop no próximo passo.

3. Execute o seguinte comando do Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Estes três passos são expressas como três ações distintas no seguinte ficheiro de fluxo de trabalho do Oozie, com o nome `workflow.xml`.

```
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
            <archive>sqljdbc41.jar</archive>
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

As duas consultas do Hive são acessadas pelo respetivo caminho no armazenamento do Azure e os restantes valores das variáveis são fornecidos pelos seguintes `job.properties` ficheiro. Este arquivo configura o fluxo de trabalho para ser executada para a data 3 de Janeiro de 2017.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

A tabela seguinte resume a cada uma das propriedades e indica onde é possível encontrar os valores para o seu próprio ambiente.

| Propriedade | Origem de valor |
| --- | --- |
| nameNode | O caminho completo para o contentor de armazenamento do Azure ligado ao seu cluster do HDInsight. |
| jobTracker | O nome de anfitrião interno para YARN do seu Active Directory cluster aceda nó. Na home page do Ambari, selecione YARN a partir da lista de serviços, em seguida, escolha o Gestor de recursos do Active Directory. O nome de anfitrião URI é apresentado na parte superior da página. Acrescente a porta 8050. |
| queueName | O nome da fila YARN utilizado durante o agendamento de ações do Hive. Deixe como predefinição. |
| oozie.use.system.libpath | Deixe como true. |
| appBase | O caminho para a subpasta no armazenamento do Azure para implementar o fluxo de trabalho do Oozie e ficheiros de suporte. |
| oozie.wf.application.path | A localização do fluxo de trabalho Oozie `workflow.xml` para executar. |
| hiveScriptLoadPartition | O caminho no armazenamento do Azure para o ficheiro de consulta do Hive `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | O caminho no armazenamento do Azure para o ficheiro de consulta do Hive `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | O nome gerado dinamicamente a utilizar para a tabela de testes. |
| hiveDataFolder | O caminho no armazenamento do Azure para os dados contidos por tabela de teste. |
| sqlDatabaseConnectionString | A JDBC sintaxe cadeia de ligação à base de dados SQL do Azure. |
| sqlDatabaseTableName | O nome da tabela na base de dados do SQL Azure na qual são inseridas linhas de resumidas. Deixe como `dailyflights`. |
| ano | O componente de ano do dia para a qual voo resumos são computados. Deixe, tal como está. |
| mês | O componente de mês do dia para a qual voo resumos são computados. Deixe, tal como está. |
| dia | O dia do componente de mês do dia para a qual voo resumos são computados. Deixe, tal como está. |

> [!NOTE]  
> Certifique-se de que atualizar a sua cópia do `job.properties` ficheiro com valores específicos ao seu ambiente, antes de poder implementar e executar o fluxo de trabalho do Oozie.

### <a name="deploy-and-run-the-oozie-workflow"></a>Implementar e executar o fluxo de trabalho do Oozie

Utilizar o SCP da sua sessão do bash para implementar o seu fluxo de trabalho do Oozie (`workflow.xml`), as consultas do Hive (`hive-load-flights-partition.hql` e `hive-create-daily-summary-table.hql`) e a configuração da tarefa (`job.properties`).  No Oozie, apenas o `job.properties` ficheiro pode existir no armazenamento local do nó principal. Todos os outros arquivos devem ser armazenados no HDFS, neste caso do armazenamento do Azure. A ação de Sqoop utilizada pelo fluxo de trabalho depende de um controlador JDBC para comunicação com a base de dados SQL, que têm de ser copiados do nó principal no HDFS.

1. Criar o `load_flights_by_day` subpasta sob o caminho do utilizador no armazenamento local do nó principal.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Copiar todos os ficheiros no diretório atual (a `workflow.xml` e `job.properties` arquivos) até o `load_flights_by_day` subpasta.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. SSH para o nó principal e navegue para o `load_flights_by_day` pasta.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Copie os ficheiros de fluxo de trabalho para HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Cópia `sqljdbc41.jar` do nó principal local para a pasta de fluxo de trabalho no HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Execute o fluxo de trabalho.

        oozie job -config job.properties -run

7. Observe o estado utilizando a consola Web do Oozie. Na Ambari, selecione **Oozie**, **ligações rápidas**e, em seguida **consola da Web de Oozie**. Sob o **tarefas de fluxo de trabalho** separador, selecione **todas as tarefas**.

    ![Fluxos de trabalho do Oozie Web Console](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Quando o estado é concluído com êxito, consulta a tabela de base de dados SQL para ver as linhas inseridas. Utilizar o portal do Azure, navegue para o painel da base de dados do SQL, selecione **ferramentas**e abra o **Editor de consultas**.

        SELECT * FROM dailyflights

Agora que o fluxo de trabalho está em execução para o dia de teste único, pode encapsular o fluxo de trabalho um coordenador que agenda o fluxo de trabalho para que seja executada diariamente.

### <a name="run-the-workflow-with-a-coordinator"></a>Executar o fluxo de trabalho com um coordenador

Para agendar este fluxo de trabalho para que seja executado diariamente (ou todos os dias num intervalo de datas), pode usar um coordenador. Um coordenador é definido por um arquivo XML, por exemplo `coordinator.xml`:

```
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

Como pode ver, a maioria do coordenador de está passando apenas informações de configuração para a instância de fluxo de trabalho. No entanto, existem alguns itens importantes para chamar.

* Ponto de 1: O `start` e `end` atributos no `coordinator-app` elemento em si controlar o intervalo de tempo durante o qual é executado o coordenador.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Ele é responsável pelo agendamento de ações dentro da `start` e `end` intervalo, de datas, de acordo com o intervalo especificado pelo `frequency` atributo. Cada ação agendada por sua vez executa o fluxo de trabalho conforme configurado. Na definição do coordenador acima, o coordenador está configurado para executar ações a partir de 1 de Janeiro de 2017 para 5 de Janeiro de 2017. A frequência está definida para 1 dia, o [linguagem de expressão de Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) expressão de frequência `${coord:days(1)}`. Isso resulta no coordenador de agendamento de uma ação (e, por conseguinte, o fluxo de trabalho) uma vez por dia. Para intervalos de datas que estão no passado, tal como neste exemplo, a ação será agendada para ser executado sem demora. Denomina-se o início da data do que uma ação está agendada para executar o *tempo nominal*. Por exemplo, para processar os dados de 1 de Janeiro de 2017, o coordenador de agendará ação com um tempo nominal de 2017-01-01T00:00:00 GMT.

* Ponto de 2: Dentro do intervalo de datas de fluxo de trabalho, o `dataset` elemento Especifica onde procurar os dados para um determinado intervalo de datas no HDFS e configura a forma como o Oozie determina se os dados estão disponíveis ainda para processamento.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    O caminho para os dados no HDFS baseia-se dinamicamente, de acordo com a expressão fornecida no `uri-template` elemento. Este coordenador, uma frequência de um dia também é utilizada com o conjunto de dados. Embora as datas de início e de fim no controle de elemento do coordenador quando as ações são agendadas (e define os tempos de nominal), o `initial-instance` e `frequency` no conjunto de dados, controlar o cálculo da data em que é utilizado para construir a `uri-template`. Neste caso, definir a instância de inicial para um dia antes do início do coordenador para se certificar de que ele seleciona o primeiro dia do valor de (1/1/2017) de dados. Cálculo da data do conjunto de dados avança do valor da `initial-instance` (12/31/2016) aprimorando em incrementos de frequência do conjunto de dados (1 dia) até encontrar a data mais recente que não passa o tempo nominal definido pelo coordenador (2017-01-01T00:00:00 GMT para a primeira ação).

    Vazio `done-flag` elemento indica que quando Oozie verifica a presença de dados de entrada no momento TAP, Oozie determina dados se disponível pela presença de um ficheiro ou diretório. Neste caso é a presença de um ficheiro csv. Se existir um ficheiro csv, Oozie pressupõe que os dados, está prontos e inicia uma instância de fluxo de trabalho para processar o ficheiro. Se não houver nenhum ficheiro csv presente, o Oozie pressupõe que os dados são ainda não preparado para e de que a execução do fluxo de trabalho entrar num Estado de espera.

* Ponto de 3: O `data-in` elemento Especifica o período de tempo específico para utilizar como o nominal tempo quando substituindo os valores no `uri-template` para o conjunto de dados associado.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Neste caso, definir a instância para a expressão `${coord:current(0)}`, que se traduz em horário nominal da ação originalmente agendada pelo coordenador. Em outras palavras, quando o coordenador de agenda a ação de execução com um tempo nominal de 01/01/2017, em seguida, 01/01/2017 é o que é utilizado para substituir o ano (2017) e variáveis de mês (01) no modelo de URI. Assim que o modelo URI é calculado para esta instância, a Oozie verifica se o diretório esperado ou o ficheiro está disponível e agenda a próxima execução do fluxo de trabalho em conformidade.

Os três pontos anteriores são combinadas para produzir uma situação em que o coordenador de agenda de processamento dos dados de origem de uma forma de dia para dia. 

* Ponto de 1: O coordenador de começa com uma data nominal 2017-01-01.

* Ponto de 2: Oozie vai procurar os dados disponíveis no `sourceDataFolder/2017-01-FlightData.csv`.

* Ponto de 3: Quando o Oozie encontra esse arquivo, ele agenda uma instância do fluxo de trabalho que irá processar os dados para 2017-01-01. Oozie, em seguida, continua o processamento de 2017-01-02. Essa avaliação se repete até, mas não incluindo 2017-01-05.

Tal como com fluxos de trabalho, a configuração de um coordenador é definida num `job.properties` arquivo, que tem um superconjunto de definições utilizadas pelo fluxo de trabalho.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

As propriedades de apenas os novos apresentadas nesta `job.properties` ficheiro são:

| Propriedade | Origem de valor |
| --- | --- |
| oozie.coord.application.path | Indica a localização do `coordinator.xml` ficheiro que contém o coordenador de Oozie para executar. |
| hiveDailyTableNamePrefix | O prefixo utilizado ao criar dinamicamente o nome da tabela de tabela de teste. |
| hiveDataFolderPrefix | O prefixo do caminho onde serão armazenadas os todas as tabelas de testes. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Implementar e executar o coordenador de Oozie

Para executar o pipeline com um coordenador, continue de maneira semelhante, como para o fluxo de trabalho, exceto que trabalhar a partir de uma pasta um nível acima na pasta que contém o fluxo de trabalho. Esta Convenção de pasta separa os coordenadores dos fluxos de trabalho no disco, para que pode associar um coordenador com fluxos de trabalho subordinado diferentes.

1. Utilize o SCP do seu computador local para copiar os arquivos de coordenador para o armazenamento local do nó principal do cluster.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. SSH para o nó principal.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Copie os ficheiros de coordenador para HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Execute o coordenador.

    ```bash
    oozie job -config job.properties -run
    ```

5. Verificar o estado utilizando a consola da Web de Oozie, este tempo, selecionando o **coordenador de tarefas** separador e, em seguida **todas as tarefas**.

    ![Tarefas de coordenador de consola Web do Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Selecione uma instância de coordenador para apresentar a lista de ações agendadas. Neste caso, deverá ver quatro ações com tempos de nominal no intervalo de 1/1/2017 para 4/1/2017.

    ![Tarefa de coordenador de consola Web do Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Cada ação nesta lista corresponde a uma instância do fluxo de trabalho que processa dias um de dados, onde o início do dia é indicado pelo tempo nominal.

## <a name="next-steps"></a>Passos Seguintes

* [Documentação do Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
