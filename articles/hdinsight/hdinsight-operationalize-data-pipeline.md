---
title: Operacionalize um pipeline de análise de dados – Azure
description: Configure e execute um pipeline de dados de exemplo que é disparado por novos dados e produz resultados concisos.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 122840614aede3ee112f8fd68cf6dabfa91fa225
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105521"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacionalizar um pipeline de análise de dados

*Pipelines de dados* em muitas soluções de análise de dados. Como o nome sugere, um pipeline de dados usa dados brutos, limpa-os e remodela-os conforme necessário e, em seguida, normalmente executa cálculos ou agregações antes de armazenar os dados processados. Os dados processados são consumidos por clientes, relatórios ou APIs. Um pipeline de dados deve fornecer resultados repetíveis, seja em um agendamento ou quando disparado por novos dados.

Este artigo descreve como colocar em operação os pipelines de dados para repetição, usando Oozie em execução em clusters Hadoop do HDInsight. O cenário de exemplo orienta você por um pipeline de dados que prepara e processa dados de série temporal de voo aérea.

No cenário a seguir, os dados de entrada são um arquivo simples que contém um lote de dados de voo para um mês. Esses dados de vôo incluem informações como o aeroporto de origem e de destino, as milhas fluem, os tempos de partida e de chegada e assim por diante. O objetivo desse pipeline é resumir o desempenho diário da companhia aérea, em que cada companhia aérea tem uma linha para cada dia com os atrasos médios de partida e chegada em minutos e o total de milhas fluir nesse dia.

| YEAR | MÊS | DAY_OF_MONTH | DA |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | SUAVIZA | 10,142229 | 7,862926 | 2644539 |
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

### <a name="provision-azure-resources"></a>Provisionar recursos do Azure

Esse pipeline requer um banco de dados SQL do Azure e um cluster HDInsight Hadoop no mesmo local. O banco de dados SQL do Azure armazena tanto o dado de resumo produzido pelo pipeline quanto o repositório de metadados Oozie.

#### <a name="provision-azure-sql-database"></a>Provisionar banco de dados SQL do Azure

1. Usando o portal do Azure, crie um novo grupo de recursos `oozie` chamado para conter todos os recursos usados por este exemplo.
2. Dentro do `oozie` grupo de recursos, provisione um banco de dados e SQL Server do Azure. Você não precisa de um banco de dados maior do que o tipo de preço S1 Standard.
3. Usando o portal do Azure, navegue até o painel de seu banco de dados SQL implantado recentemente e selecione **ferramentas**.

    ![Ícone de botão de ferramentas de BD SQL do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-sql-db-tools-button.png)

4. Selecione **Editor de consultas**.

    ![Visualização do editor de consultas do BD SQL](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor1.png)

5. No painel **Editor de consultas** , selecione **logon**.

    ![Janela de logon do BD SQL do editor de consultas](./media/hdinsight-operationalize-data-pipeline/sql-db-login-window1.png)

6. Insira suas credenciais do banco de dados SQL e selecione **OK**.

   ![Parâmetros de logon do BD SQL do editor de consultas](./media/hdinsight-operationalize-data-pipeline/sql-db-login-window2.png)

7. Na área de texto editor de consultas, insira as seguintes instruções SQL para criar `dailyflights` a tabela que armazenará os dados resumidos de cada execução do pipeline.

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

8. Selecione **executar** para executar as instruções SQL.

    ![Botão Executar do BD SQL do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-sql-db-run-button.png)

O banco de dados SQL do Azure agora está pronto.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Provisionar um cluster HDInsight Hadoop

1. Na portal do Azure, selecione **+ novo** e pesquise por HDInsight.
2. Selecione **Criar**.
3. No painel básico, forneça um nome exclusivo para o cluster e escolha sua assinatura do Azure.

    ![Nome e assinatura do cluster HDInsight](./media/hdinsight-operationalize-data-pipeline/cluster-name-subscription.png)

4. No painel **tipo de cluster** , selecione o tipo de cluster **Hadoop** , o sistema operacional **Linux** e a versão mais recente do cluster HDInsight. Deixe a **camada de cluster** em **padrão**.

    ![Tipo de configuração de cluster portal do Azure](./media/hdinsight-operationalize-data-pipeline/hdinsight-cluster-type.png)

5. Escolha **selecionar** para aplicar a seleção de tipo de cluster.
6. Preencha o painel **básico** fornecendo uma senha de logon e selecionando `oozie` o grupo de recursos na lista e, em seguida, selecione **Avançar**.

    ![Painel criar noções básicas do cluster portal do Azure](./media/hdinsight-operationalize-data-pipeline/hdinsight-basics-pane.png)

7. No painel **armazenamento** , deixe o tipo de armazenamento primário definido como **armazenamento do Azure**, selecione **criar novo**e forneça um nome para a nova conta.

    ![Configurações da conta de armazenamento do HDInsight](./media/hdinsight-operationalize-data-pipeline/storage-account-settings.png)

8. Para as **configurações de metastore**, em **selecionar um banco de dados SQL para Hive**, escolha o banco de dados que você criou anteriormente.

    ![Configurações de metastore do hive do HDInsight](./media/hdinsight-operationalize-data-pipeline/hive-metastore-settings.png)

9. Selecione **autenticar banco de dados SQL**.

    ![Autenticação do metastore do hive do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Insira o nome de usuário e a senha do banco de dados SQL e escolha **selecionar**.

       ![Logon de autenticação do metastore do hive do HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. De volta ao painel **configurações do metastore** , selecione o banco de dados para o repositório de metadados Oozie e autentique-o como fez anteriormente.

       ![Configurações de metastore portal do Azure](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Selecione **Seguinte**.
13. No painel **Resumo** , selecione **criar** para implantar o cluster.

### <a name="verify-ssh-tunneling-setup"></a>Verificar a configuração de túnel SSH

Para usar o console Web do Oozie para exibir o status de suas instâncias de coordenador e fluxo de trabalho, configure um túnel SSH para seu cluster HDInsight. Para obter mais informações, consulte [SSH Tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Você também pode usar o Chrome com a extensão de [proxy Foxy](https://getfoxyproxy.org/) para procurar os recursos da Web do cluster no túnel SSH. Configure-o para fazer o proxy de todas `localhost` as solicitações por meio do host na porta do túnel 9876. Essa abordagem é compatível com o subsistema do Windows para Linux, também conhecido como bash no Windows 10.

1. Execute o seguinte comando para abrir um túnel SSH para o cluster:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Verifique se o túnel está operacional navegando até Ambari em seu nó de cabeçalho navegando até:

    http:\//headnodehost:8080

3. Para acessar o **console Web do Oozie** de dentro de Ambari, selecione **Oozie**, **links rápidos**e, em seguida, selecione **console Web do Oozie**.

### <a name="configure-hive"></a>Configurar o hive

1. Baixe um arquivo CSV de exemplo que contém dados de voo para um mês. Baixe seu arquivo `2017-01-FlightData.zip` zip do [repositório GitHub do HDInsight](https://github.com/hdinsight/hdinsight-dev-guide) e descompacte-o no `2017-01-FlightData.csv`arquivo CSV. 

2. Copie esse arquivo CSV até a conta de armazenamento do Azure anexada ao seu cluster HDInsight e coloque- `/example/data/flights` o na pasta.

Você pode copiar o arquivo usando o SCP na `bash` sessão do Shell.

1. Use o SCP para copiar os arquivos do computador local para o armazenamento local do nó principal do cluster HDInsight.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Use o comando HDFS para copiar o arquivo do armazenamento local do nó de cabeçalho para o armazenamento do Azure.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Os dados de exemplo agora estão disponíveis. No entanto, o pipeline requer duas tabelas Hive para processamento, uma para os dados`rawFlights`de entrada () e outra para os dados`flights`resumidos (). Crie essas tabelas no Ambari da seguinte maneira.

1. Faça logon no Ambari navegando para http:\//headnodehost: 8080.

2. Na lista de serviços, selecione **Hive**.

    ![Lista de serviços Apache Ambari selecionando o hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Selecione **ir para exibição** ao lado do rótulo de exibição do hive 2,0.

    ![Lista de Resumo de Apache Hive do Apache Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Na área de texto de consulta, Cole as instruções a seguir para `rawFlights` criar a tabela. A `rawFlights` tabela fornece um esquema em leitura para os arquivos CSV dentro da `/example/data/flights` pasta no armazenamento do Azure.

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

6. Para criar a `flights` tabela, substitua o texto na área de texto da consulta pelas instruções a seguir. A `flights` tabela é uma tabela gerenciada pelo Hive que particiona os dados carregados nele por ano, mês e dia do mês. Esta tabela conterá todos os dados de vôo históricos, com a granularidade mais baixa presente nos dados de origem de uma linha por voo.

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

7. Selecione **executar** para criar a tabela.

### <a name="create-the-oozie-workflow"></a>Criar o fluxo de trabalho do Oozie

Os pipelines normalmente processam dados em lotes por um determinado intervalo de tempo. Nesse caso, o pipeline processa os dados de vôo diariamente. Essa abordagem permite que os arquivos CSV de entrada cheguem diariamente, semanalmente, mensalmente ou anualmente.

O fluxo de trabalho de exemplo processa os dados de vôo dia a dia, em três etapas principais:

1. Execute uma consulta de Hive para extrair os dados do intervalo de datas desse dia do arquivo CSV de origem representado pela `rawFlights` tabela e inserir os dados `flights` na tabela.
2. Execute uma consulta de Hive para criar dinamicamente uma tabela de preparo no hive para o dia, que contém uma cópia dos dados de vôo resumidos por dia e pela operadora.
3. Use o Apache Sqoop para copiar todos os dados da tabela de preparo diária no hive para a tabela `dailyflights` de destino no banco de dados SQL do Azure. O Sqoop lê as linhas de origem dos dados por trás da tabela do hive que reside no armazenamento do Azure e carrega-as no SQL Database usando uma conexão JDBC.

Essas três etapas são coordenadas por um fluxo de trabalho do Oozie. 

1. Crie uma consulta no arquivo `hive-load-flights-partition.hql`.

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

    As variáveis Oozie usam a `${variableName}`sintaxe. Essas variáveis são definidas no `job.properties` arquivo, conforme descrito em uma etapa subsequente. Oozie substitui os valores reais em tempo de execução.

2. Crie uma consulta no arquivo `hive-create-daily-summary-table.hql`.

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

    Essa consulta cria uma tabela de preparo que armazenará apenas os dados resumidos de um dia, anote a instrução SELECT que computa os atrasos médios e o total de distância da portadora por dia. Os dados inseridos nessa tabela armazenados em um local conhecido (o caminho indicado pela variável hiveDataFolder) para que ele possa ser usado como a origem para Sqoop na próxima etapa.

3. Execute o comando Sqoop a seguir.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Essas três etapas são expressas como três ações separadas no seguinte arquivo de fluxo de trabalho `workflow.xml`Oozie, chamado.

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

As duas consultas de Hive são acessadas por seu caminho no armazenamento do Azure e os valores de variáveis restantes são `job.properties` fornecidos pelo arquivo a seguir. Este arquivo configura o fluxo de trabalho a ser executado para a data 3 de janeiro de 2017.

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

A tabela a seguir resume cada uma das propriedades e indica onde você pode encontrar os valores para seu próprio ambiente.

| Propriedade | Origem do valor |
| --- | --- |
| nameNode | O caminho completo para o contêiner de armazenamento do Azure anexado ao seu cluster HDInsight. |
| jobTracker | O nome do host interno para o nó de cabeçalho do YARN do seu cluster ativo. Na home page Ambari, selecione YARN na lista de serviços e escolha active Resource Manager. O URI do nome do host é exibido na parte superior da página. Acrescente a porta 8050. |
| queueName | O nome da fila YARN usada ao agendar as ações do hive. Deixe como padrão. |
| oozie.use.system.libpath | Deixe como verdadeiro. |
| appBase | O caminho para a subpasta no armazenamento do Azure em que você implanta o fluxo de trabalho do Oozie e os arquivos de suporte. |
| oozie.wf.application.path | O local do fluxo de trabalho `workflow.xml` do Oozie a ser executado. |
| hiveScriptLoadPartition | O caminho no armazenamento do Azure para o arquivo `hive-load-flights-partition.hql`de consulta do hive. |
| hiveScriptCreateDailyTable | O caminho no armazenamento do Azure para o arquivo `hive-create-daily-summary-table.hql`de consulta do hive. |
| hiveDailyTableName | O nome gerado dinamicamente a ser usado para a tabela de preparo. |
| hiveDataFolder | O caminho no armazenamento do Azure para os dados contidos na tabela de preparo. |
| sqlDatabaseConnectionString | A cadeia de conexão de sintaxe JDBC para o banco de dados SQL do Azure. |
| sqlDatabaseTableName | O nome da tabela no banco de dados SQL do Azure no qual as linhas de resumo são inseridas. Deixe como `dailyflights`. |
| ano | O componente de ano do dia para o qual os resumos de voo são computados. Deixe como está. |
| mês | O componente de mês do dia para o qual os resumos de voo são computados. Deixe como está. |
| dia | O componente de dia do mês do dia para o qual os resumos de voo são computados. Deixe como está. |

> [!NOTE]  
> Certifique-se de atualizar sua cópia do `job.properties` arquivo com os valores específicos para seu ambiente, antes de implantar e executar o fluxo de trabalho do Oozie.

### <a name="deploy-and-run-the-oozie-workflow"></a>Implantar e executar o fluxo de trabalho do Oozie

Use o SCP de sua sessão de bash para implantar seu Oozie`workflow.xml`Workflow (), as consultas`hive-load-flights-partition.hql` de `hive-create-daily-summary-table.hql`Hive (e) e a`job.properties`configuração do trabalho ().  No Oozie, somente o `job.properties` arquivo pode existir no armazenamento local do cabeçalho. Todos os outros arquivos devem ser armazenados no HDFS, nesse caso, o armazenamento do Azure. A ação Sqoop usada pelo fluxo de trabalho depende de um driver JDBC para se comunicar com o banco de dados SQL, que deve ser copiado do nó principal para o HDFS.

1. Crie a `load_flights_by_day` subpasta sob o caminho do usuário no armazenamento local do nó principal.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Copie todos os arquivos no diretório atual (os `workflow.xml` arquivos `job.properties` e) até a `load_flights_by_day` subpasta.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. Use ssh em seu nó principal e navegue até `load_flights_by_day` a pasta.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Copiar arquivos de fluxo de trabalho para HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Copie `sqljdbc41.jar` do nó de cabeçalho local para a pasta de fluxo de trabalho no HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Execute o fluxo de trabalho.

        oozie job -config job.properties -run

7. Observe o status usando o console Web do Oozie. Em Ambari, selecione **Oozie**, **links rápidos**e, em seguida, **console Web do Oozie**. Na guia **trabalhos de fluxo** de trabalho, selecione **todos os trabalhos**.

    ![fluxos de trabalho do console Web do HDI oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Quando o status for bem-sucedido, consulte a tabela do banco de dados SQL para exibir as linhas inseridas. Usando o portal do Azure, navegue até o painel do banco de dados SQL, selecione **ferramentas**e abra o **Editor de consultas**.

        SELECT * FROM dailyflights

Agora que o fluxo de trabalho está em execução para o dia de teste único, você pode encapsular esse fluxo de trabalho com um coordenador que agenda o fluxo de trabalho para que ele seja executado diariamente.

### <a name="run-the-workflow-with-a-coordinator"></a>Executar o fluxo de trabalho com um coordenador

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

* Ponto 1: Os `start` atributos `end` e no`coordinator-app` próprio elemento controlam o intervalo de tempo durante o qual o coordenador é executado.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Um coordenador é responsável por agendar ações dentro `start` do `end` intervalo de datas e, de acordo com `frequency` o intervalo especificado pelo atributo. Cada ação agendada, por sua vez, executa o fluxo de trabalho conforme configurado. Na definição de coordenador acima, o coordenador está configurado para executar ações de 1º de janeiro de 2017 a 5 de janeiro de 2017. A frequência é definida como 1 dia pela expressão `${coord:days(1)}`de frequência da linguagem da [expressão Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) . Isso resulta no agendamento de um coordenador de uma ação (e, portanto, do fluxo de trabalho) uma vez por dia. Para intervalos de datas que estão no passado, como neste exemplo, a ação será agendada para ser executada sem atraso. O início da data a partir da qual uma ação está agendada para execução é chamada de *tempo nominal*. Por exemplo, para processar os dados de 1º de janeiro de 2017, o coordenador agendará a ação com um tempo nominal de 2017-01-01T00:00:00 GMT.

* Ponto 2: Dentro do intervalo de datas do fluxo de trabalho `dataset` , o elemento especifica onde procurar no HDFS os dados de um intervalo de datas específico e configura como o Oozie determina se os dados ainda estão disponíveis para processamento.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    O caminho para os dados no HDFS é criado dinamicamente de acordo com a expressão fornecida no `uri-template` elemento. Nesse coordenador, uma frequência de um dia também é usada com o conjunto de um. Embora as datas de início e de término no elemento de coordenador controlem quando as ações são agendadas (e definem seus `initial-instance` horários `frequency` nominais), o e no conjunto de dados controlam o cálculo da data usada na construção do `uri-template`. Nesse caso, defina a instância inicial como um dia antes do início do coordenador para garantir que ela pegue o valor de dados do primeiro dia (1/1/2017). O cálculo de data do conjunto de dados é enviado para `initial-instance` frente a partir do valor de (12/31/2016) avançando em incrementos de frequência de DataSet (1 dia) até encontrar a data mais recente que não passa o tempo nominal definido pelo coordenador (2017-01-01T00:00:00 GMT para a primeira ação).

    O elemento `done-flag` Empty indica que quando o Oozie verifica a presença de dados de entrada na hora indicada, o Oozie determina os dados, se disponíveis por presença de um diretório ou arquivo. Nesse caso, é a presença de um arquivo CSV. Se um arquivo CSV estiver presente, o Oozie assumirá que os dados estão prontos e iniciará uma instância de fluxo de trabalho para processar o arquivo. Se não houver nenhum arquivo CSV presente, o Oozie assumirá que os dados ainda não estão prontos e que a execução do fluxo de trabalho entrará em um estado de espera.

* Ponto 3: O `data-in` elemento Especifica o carimbo de data/hora específico a ser usado como o tempo nominal `uri-template` ao substituir os valores em para o conjunto de dado associado.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Nesse caso, defina a instância como a expressão `${coord:current(0)}`, que se traduz em usar o tempo nominal da ação como agendado originalmente pelo coordenador. Em outras palavras, quando o coordenador agenda a ação a ser executada com um tempo nominal de 01/01/2017, 01/01/2017 é o que é usado para substituir as variáveis YEAR (2017) e MONTH (01) no modelo de URI. Depois que o modelo de URI é computado para essa instância, Oozie verifica se o diretório ou arquivo esperado está disponível e agenda a próxima execução do fluxo de trabalho de acordo.

Os três pontos anteriores são combinados para gerar uma situação em que o coordenador agenda o processamento dos dados de origem diariamente. 

* Ponto 1: O coordenador começa com uma data nominal de 2017-01-01.

* Ponto 2: Oozie procura dados disponíveis em `sourceDataFolder/2017-01-FlightData.csv`.

* Ponto 3: Quando o Oozie encontra esse arquivo, ele agenda uma instância do fluxo de trabalho que processará os dados para 2017-01-01. Oozie, em seguida, continua o processamento para 2017-01-02. Essa avaliação repete até, mas não incluindo 2017-01-05.

Assim como acontece com os fluxos de trabalho, a configuração de um coordenador `job.properties` é definida em um arquivo, que tem um superconjunto das configurações usadas pelo fluxo de trabalho.

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

As únicas novas propriedades apresentadas neste `job.properties` arquivo são:

| Propriedade | Origem do valor |
| --- | --- |
| oozie.coord.application.path | Indica o local do `coordinator.xml` arquivo que contém o coordenador Oozie a ser executado. |
| hiveDailyTableNamePrefix | O prefixo usado ao criar dinamicamente o nome da tabela de preparo. |
| hiveDataFolderPrefix | O prefixo do caminho onde todas as tabelas de preparo serão armazenadas. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Implantar e executar o coordenador de Oozie

Para executar o pipeline com um coordenador, prossiga de maneira semelhante para o fluxo de trabalho, exceto que você trabalha de uma pasta um nível acima da pasta que contém o fluxo de trabalho. Essa convenção de pasta separa os coordenadores dos fluxos de trabalho no disco, para que você possa associar um coordenador a diferentes fluxos de trabalho filho.

1. Use o SCP do computador local para copiar os arquivos do coordenador para o armazenamento local do nó principal do cluster.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. SSH em seu nó de cabeçalho.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
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

## <a name="next-steps"></a>Passos Seguintes

* [Documentação do Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
