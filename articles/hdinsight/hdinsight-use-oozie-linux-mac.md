---
title: Usar fluxos de trabalho Oozie do Hadoop no Azure HDInsight baseado em Linux
description: Use o Oozie do Hadoop no HDInsight baseado em Linux. Saiba como definir um fluxo de trabalho do Oozie e enviar uma tarefa do Oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: b0cb5f9fa0a0bc64b38225fba03568cf31021572
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181089"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Usar o Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux

Saiba como usar o Apache Oozie com o Apache Hadoop no Azure HDInsight. Oozie é um sistema de fluxo de trabalho e coordenação que gerencia trabalhos do Hadoop. O Oozie é integrado à pilha do Hadoop e dá suporte aos seguintes trabalhos:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Você também pode usar o Oozie para agendar trabalhos que são específicos de um sistema, como programas Java ou scripts de Shell.

> [!NOTE]  
> Outra opção para definir fluxos de trabalho com o HDInsight é usar Azure Data Factory. Para saber mais sobre Data Factory, confira [usar o Apache Pig e o Apache Hive com data Factory][azure-data-factory-pig-hive]. Para usar o Oozie em clusters com Enterprise Security Package, consulte [executar o Apache Oozie em clusters Hadoop do HDInsight com Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop no HDInsight**. Consulte [introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Um cliente SSH**. Consulte [conectar-se ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Um banco de dados SQL do Azure**.  Consulte [criar um banco de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started.md).  Este artigo usa um banco de `oozietest`dados denominado.

* O [esquema de URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário de clusters. Isso seria `wasb://` para o armazenamento do Azure `abfs://` , por Azure data Lake Storage Gen2 `adl://` ou para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure, `wasbs://`o URI será. Consulte também a [transferência segura](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Fluxo de trabalho de exemplo

O fluxo de trabalho usado neste documento contém duas ações. Ações são definições para tarefas, como executar Hive, Sqoop, MapReduce ou outros processos:

![Diagrama de fluxo de trabalho do HDInsight oozie](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Uma ação do hive executa um script HiveQL para extrair registros do `hivesampletable` que está incluído no HDInsight. Cada linha de dados descreve uma visita a partir de um dispositivo móvel específico. O formato do registro aparece como o texto a seguir:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    O script do hive usado neste documento conta o total de visitas para cada plataforma, como Android ou iPhone, e armazena as contagens em uma nova tabela do hive.

    Para obter mais informações sobre o Hive, consulte [usar o Apache Hive com o HDInsight][hdinsight-use-hive].

2. Uma ação Sqoop exporta o conteúdo da nova tabela Hive para uma tabela criada no banco de dados SQL do Azure. Para obter mais informações sobre Sqoop, consulte [usar o Apache Sqoop com o HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Para versões do Oozie com suporte em clusters HDInsight, consulte [novidades nas versões do cluster Hadoop fornecidas pelo HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Criar o diretório de trabalho

Oozie espera que você armazene todos os recursos necessários para um trabalho no mesmo diretório. Este exemplo usa `wasbs:///tutorials/useoozie`. Para criar esse diretório, conclua as seguintes etapas:

1. Edite o código abaixo para `sshuser` substituir pelo nome de usuário SSH do cluster e substitua `clustername` pelo nome do cluster.  Em seguida, insira o código para se conectar ao cluster HDInsight [usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. Para criar o diretório, use o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > O `-p` parâmetro causa a criação de todos os diretórios no caminho. O `data` diretório é usado para manter os dados usados `useooziewf.hql` pelo script.

3. Edite o código abaixo para `username` substituir pelo seu nome de usuário SSH.  Para garantir que o Oozie possa representar sua conta de usuário, use o seguinte comando:

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > Você pode ignorar os erros que indicam que o usuário já é um membro `users` do grupo.

## <a name="add-a-database-driver"></a>Adicionar um driver de banco de dados

Como esse fluxo de trabalho usa o Sqoop para exportar dados para o banco de dado SQL, você deve fornecer uma cópia do driver JDBC usado para interagir com o banco de dados SQL. Para copiar o driver JDBC para o diretório de trabalho, use o seguinte comando da sessão SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Verifique o driver JDBC real que existe em `/usr/share/java/`.

Se o fluxo de trabalho tiver usado outros recursos, como um jar que contém um aplicativo MapReduce, você precisará adicionar esses recursos também.

## <a name="define-the-hive-query"></a>Definir a consulta do hive

Use as etapas a seguir para criar um script HiveQL (linguagem de consulta do hive) que define uma consulta. Você usará a consulta em um fluxo de trabalho do Oozie, mais adiante neste documento.

1. Na conexão SSH, use o seguinte comando para criar um arquivo chamado `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Depois que o editor do nano do GNU for aberto, use a seguinte consulta como o conteúdo do arquivo:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Há duas variáveis usadas no script:

   * `${hiveTableName}`: Contém o nome da tabela a ser criada.

   * `${hiveDataFolder}`: Contém o local para armazenar os arquivos de dados da tabela.

     O arquivo de definição de fluxo de trabalho, Workflow. xml neste artigo, passa esses valores para esse script HiveQL em tempo de execução.

4. Para salvar o arquivo, selecione CTRL + X, digite `Y`e, em seguida, selecione **Enter**.  

5. Use o seguinte comando para copiar `useooziewf.hql` para `wasbs:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Esse comando armazena o `useooziewf.hql` arquivo no armazenamento compatível com HDFS para o cluster.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

As definições de fluxo de trabalho Oozie são escritas na hPDL (linguagem de definição de processo) do Hadoop, que é uma linguagem de definição de processo XML. Use as etapas a seguir para definir o fluxo de trabalho:

1. Use a seguinte instrução para criar e editar um novo arquivo:

    ```bash
    nano workflow.xml
    ```

2. Depois que o editor do nano for aberto, insira o seguinte XML como o conteúdo do arquivo:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
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
            <arg>${hiveDataFolder}</arg>
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

    Há duas ações definidas no fluxo de trabalho:

   * `RunHiveScript`: Essa ação é a ação inicial e executa o `useooziewf.hql` script do hive.

   * `RunSqoopExport`: Essa ação exporta os dados criados do script Hive para um banco de dados SQL usando Sqoop. Essa ação só será executada se `RunHiveScript` a ação for bem-sucedida.

     O fluxo de trabalho tem várias entradas, `${jobTracker}`como. Você substituirá essas entradas pelos valores usados na definição de trabalho. Você criará a definição de trabalho mais adiante neste documento.

     Observe também a `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` entrada na seção Sqoop. Essa entrada instrui o Oozie a disponibilizar esse arquivo para Sqoop quando essa ação é executada.

3. Para salvar o arquivo, selecione CTRL + X, digite `Y`e, em seguida, selecione **Enter**.  

4. Use o seguinte comando para copiar o `workflow.xml` arquivo para `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Criar uma tabela

> [!NOTE]  
> Há várias maneiras de se conectar ao banco de dados SQL para criar uma tabela. Os passos seguintes utilizam [FreeTDS](https://www.freetds.org/) do cluster do HDInsight.

1. Use o seguinte comando para instalar o FreeTDS no cluster HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Edite o código abaixo para `<serverName>` substituir pelo nome do SQL Server do Azure `<sqlLogin>` e pelo logon do Azure SQL Server.  Insira o comando para se conectar ao banco de dados SQL de pré-requisito.  Digite a senha no prompt.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Você recebe uma saída semelhante ao seguinte texto:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Na linha de comandos `1>`, introduza as seguintes linhas:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Essas instruções criam uma tabela, chamada `mobiledata`, que é usada pelo fluxo de trabalho.

    Para verificar se a tabela foi criada, use os seguintes comandos:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Você verá uma saída semelhante ao seguinte texto:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Saia do utilitário TSQL digitando `exit` `1>` no prompt.

## <a name="create-the-job-definition"></a>Criar a definição de trabalho

A definição de trabalho descreve onde encontrar o Workflow. xml. Ele também descreve onde encontrar outros arquivos usados pelo fluxo de trabalho, `useooziewf.hql`como. Além disso, ele define os valores para as propriedades usadas no fluxo de trabalho e os arquivos associados.

1. Para obter o endereço completo do armazenamento padrão, use o comando a seguir. Esse endereço é usado no arquivo de configuração que você cria na próxima etapa.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Esse comando retorna informações como o XML a seguir:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Se o cluster HDInsight usar o armazenamento do Azure como o armazenamento padrão `<value>` , o conteúdo do `wasbs://`elemento começará com. Se Azure Data Lake Storage Gen1 for usado em vez disso, ele `adl://`começará com. Se Azure Data Lake Storage Gen2 for usado, ele começará `abfs://`com.

    Salve o conteúdo do `<value>` elemento, pois ele é usado nas próximas etapas.

2. Edite o XML abaixo da seguinte maneira:

    |Valor do espaço reservado| Valor substituído|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Valor recebido da etapa 1.|
    |ADM| Seu nome de logon para o cluster HDInsight, se não for admin.|
    |ServerName| Nome do servidor do banco de dados SQL do Azure.|
    |sqlLogin| Logon do servidor do banco de dados SQL do Azure.|
    |sqlPassword| Senha de logon do servidor do banco de dados SQL do Azure.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    A maioria das informações neste arquivo é usada para preencher os valores usados nos arquivos Workflow. xml ou ooziewf. HQL, `${nameNode}`como.  Se o caminho for um `wasbs` caminho, você deverá usar o caminho completo. Não a encurte para apenas `wasbs:///`. A `oozie.wf.application.path` entrada define onde encontrar o arquivo Workflow. xml. Esse arquivo contém o fluxo de trabalho que foi executado por este Job.

3. Para criar a configuração de definição de trabalho Oozie, use o seguinte comando:

    ```bash
    nano job.xml
    ```

4. Depois que o editor do nano for aberto, Cole o XML editado como o conteúdo do arquivo.

5. Para salvar o arquivo, selecione CTRL + X, digite `Y`e, em seguida, selecione **Enter**.

## <a name="submit-and-manage-the-job"></a>Enviar e gerenciar o trabalho

As etapas a seguir usam o comando Oozie para enviar e gerenciar fluxos de trabalho do Oozie no cluster. O comando Oozie é uma interface amigável sobre a [API REST do Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Ao usar o comando Oozie, você deve usar o FQDN para o nó principal do HDInsight. Esse FQDN só pode ser acessado do cluster ou se o cluster estiver em uma rede virtual do Azure, de outros computadores na mesma rede.

1. Para obter a URL para o serviço Oozie, use o seguinte comando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Isso retorna informações como o XML a seguir:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` parte é a URL a ser usada com o comando Oozie.

2. Edite o código para substituir a URL por aquela que você recebeu anteriormente. Para criar uma variável de ambiente para a URL, use o seguinte, para que você não precise inseri-la para cada comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Para enviar o trabalho, use o seguinte:

    ```bash
    oozie job -config job.xml -submit
    ```

    Esse comando carrega as informações do trabalho `job.xml` do e as envia para o Oozie, mas não a executa.

    Depois que o comando for concluído, ele deverá retornar a ID do trabalho, por exemplo `0000005-150622124850154-oozie-oozi-W`,. Essa ID é usada para gerenciar o trabalho.

4. Edite o código abaixo para `<JOBID>` substituir pela ID retornada na etapa anterior.  Para exibir o status do trabalho, use o seguinte comando:

    ```bash
    oozie job -info <JOBID>
    ```

    Isso retorna informações como o texto a seguir:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Este trabalho tem um status de `PREP`. Esse status indica que o trabalho foi criado, mas não iniciado.

5. Edite o código abaixo para `<JOBID>` substituir pela ID retornada anteriormente.  Para iniciar o trabalho, use o seguinte comando:

    ```bash
    oozie job -start <JOBID>
    ```

    Se você verificar o status após esse comando, ele estará em um estado de execução e as informações serão retornadas para as ações dentro do trabalho.  O trabalho levará alguns minutos para ser concluído.

6. Edite o código abaixo para `<serverName>` substituir pelo nome do SQL Server do Azure `<sqlLogin>` e pelo logon do Azure SQL Server.  Depois que a tarefa for concluída com êxito, você poderá verificar se os dados foram gerados e exportados para a tabela do SQL Database usando o comando a seguir.  Digite a senha no prompt.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    `1>` No prompt, insira a seguinte consulta:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    As informações retornadas são semelhantes ao seguinte texto:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para obter mais informações sobre o comando Oozie, consulte [ferramenta de linha de comando Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API REST do Oozie

Com a API REST do Oozie, você pode criar suas próprias ferramentas que funcionam com o Oozie. Veja a seguir as informações específicas do HDInsight sobre o uso da API REST do amOozie:

* **URI**: Você pode acessar a API REST de fora do cluster em `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autenticação**: Para autenticar, use a API da conta HTTP do cluster (admin) e da senha. Por exemplo:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Para obter mais informações sobre como usar a API REST do amOozie, consulte [API de serviços Web do Apache Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interface do usuário da Web do amOozie

A interface do usuário da Web do amOozie fornece uma exibição baseada na Web para o status de trabalhos do Oozie no cluster. Com a interface do usuário da Web, você pode exibir as seguintes informações:

   * Estado da tarefa
   * Definição da tarefa
   * Configuração
   * Um grafo das ações no trabalho
   * Logs para o trabalho

Você também pode exibir os detalhes das ações em um trabalho.

Para acessar a interface do usuário da Web do amOozie, conclua as seguintes etapas:

1. Crie um túnel SSH para o cluster HDInsight. Para obter mais informações, consulte [usar o túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Depois de criar um túnel, abra a interface do usuário da Web do amAmbari no `http://headnodehost:8080`seu navegador da Web usando o URI.

3. No lado esquerdo da página, selecione **Oozie** > **links** > rápidos**Oozie interface do usuário da Web**.

    ![Etapas da interface do usuário da Web do Apache Ambari oozie](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. O padrão da interface do usuário da Web do amOozie exibe os trabalhos de fluxo de trabalho em execução. Para ver todos os trabalhos de fluxo de trabalho, selecione **todos os trabalhos**.

    ![Trabalhos de fluxo de trabalho do console Web do Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Para exibir mais informações sobre um trabalho, selecione o trabalho.

    ![Informações do trabalho do Apache Oozie do HDInsight](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Na guia **informações do trabalho** , você pode ver as informações básicas do trabalho e as ações individuais dentro do trabalho. Você pode usar as guias na parte superior para exibir a **definição de trabalho**, **configuração de trabalho**, acessar o log de **trabalho**ou exibir um grafo direcionado acíclico (DAG) do trabalho em DAG de **trabalho**.

   * **Log de trabalho**: Selecione o botão **obter logs** para obter todos os logs do trabalho ou use o campo **Inserir filtro de pesquisa** para filtrar os logs.

       ![Log de trabalho do Apache Oozie no HDInsight](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **DAG do trabalho**: O DAG é uma visão geral gráfica dos caminhos de dados feitos por meio do fluxo de trabalho.

       ![Dag do trabalho do Apache Oozie do HDInsight](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Se você selecionar uma das ações na guia **informações do trabalho** , ele exibirá informações para a ação. Por exemplo, selecione a ação **RunSqoopExport** .

    ![Informações de ação de trabalho do HDInsight oozie](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Você pode ver detalhes da ação, como um link para a URL do **console**. Use este link para exibir informações do rastreador de trabalho para o trabalho.

## <a name="schedule-jobs"></a>Agendar tarefas

Você pode usar o coordenador para especificar uma frequência de início, de término e de ocorrência para trabalhos. Para definir uma agenda para o fluxo de trabalho, conclua as seguintes etapas:

1. Use o seguinte comando para criar um arquivo chamado **Coordinator. xml**:

    ```bash
    nano coordinator.xml
    ```

    Use o seguinte XML como o conteúdo do arquivo:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > As `${...}` variáveis são substituídas por valores na definição de trabalho em tempo de execução. As variáveis são:
    >
    > * `${coordFrequency}`: O tempo entre as instâncias em execução do trabalho.
    > * `${coordStart}`: A hora de início do trabalho.
    > * `${coordEnd}`: A hora de término do trabalho.
    > * `${coordTimezone}`: Os trabalhos do coordenador estão em um fuso horário fixo sem horário de verão, normalmente representado pelo uso de UTC. Esse fuso horário é chamado de *Oozie de processamento de fuso horário.*
    > * `${wfPath}`: O caminho para o Workflow. xml.

2. Para salvar o arquivo, selecione CTRL + X, digite `Y`e, em seguida, selecione **Enter**.

3. Para copiar o arquivo para o diretório de trabalho para esse trabalho, use o seguinte comando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Para modificar o `job.xml` arquivo criado anteriormente, use o seguinte comando:

    ```bash
    nano job.xml
    ```

    Efetue as seguintes alterações:

   * Para instruir o Oozie a executar o arquivo do coordenador em vez do `<name>oozie.wf.application.path</name>` fluxo `<name>oozie.coord.application.path</name>`de trabalho, altere para.

   * Para definir a `workflowPath` variável usada pelo coordenador, adicione o seguinte XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Substitua o `wasbs://mycontainer@mystorageaccount.blob.core.windows` texto pelo valor usado nas outras entradas no arquivo Job. xml.

   * Para definir o início, o fim e a frequência do coordenador, adicione o seguinte XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Esses valores definem a hora de início como 12:00 PM em 10 de maio de 2018 e a hora de término como 12 de maio de 2018. O intervalo para executar esse trabalho é definido como diário. A frequência é em minutos, por isso 24 horas x 60 minutos = 1440 minutos. Por fim, o fuso horário é definido como UTC.

5. Para salvar o arquivo, selecione CTRL + X, digite `Y`e, em seguida, selecione **Enter**.

6. Para enviar e iniciar o trabalho, use o seguinte comando:

    ```bash
    oozie job -config job.xml -run
    ```

7. Se você for para a interface do usuário da Web do amOozie e selecionar a guia **trabalhos do coordenador** , verá informações como na imagem a seguir:

    ![Guia de trabalhos do coordenador do console Web do Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    A entrada de **materialização seguinte** conterá a próxima vez que o trabalho for executado.

8. Assim como o trabalho anterior de workflow, se você selecionar a entrada de trabalho na interface do usuário da Web, ele exibirá informações sobre o trabalho:

    ![Informações do trabalho do coordenador do Apache Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Essa imagem mostra apenas as execuções bem-sucedidas do trabalho, e não as ações individuais dentro do Workflow agendado. Para ver as ações individuais, selecione uma das entradas de **ação** .

    ![Guia de informações do trabalho do console Web do OOzie](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Resolução de problemas

Com a interface do usuário do amOozie, você pode exibir logs do Oozie. A interface do usuário do Oozie também contém links para os logs do JobTracker para as tarefas do MapReduce que foram iniciadas pelo fluxo de trabalho. O padrão para solução de problemas deve ser:

   1. Exiba o trabalho na interface do usuário da Web do amOozie.

   2. Se houver um erro ou falha para uma ação específica, selecione a ação para ver se o campo **mensagem de erro** fornece mais informações sobre a falha.

   3. Se estiver disponível, use a URL da ação para exibir mais detalhes, como os logs de JobTracker, para a ação.

Estes são os erros específicos que você pode encontrar e como resolvê-los.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Não é possível inicializar o cluster

**Sintomas**: O status do trabalho muda para **suspenso**. Os detalhes do trabalho mostram o `RunHiveScript` status como **START_MANUAL**. A seleção da ação exibe a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Motivo**: Os endereços do armazenamento de BLOBs do Azure usados no arquivo **Job. xml** não contêm o contêiner de armazenamento ou o nome da conta de armazenamento. O formato do endereço de armazenamento de `wasbs://containername@storageaccountname.blob.core.windows.net`BLOBs deve ser.

**Resolução**: Altere os endereços de armazenamento de BLOBs que o trabalho usa.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie não tem permissão para representar &lt;o usuário&gt;

**Sintomas**: O status do trabalho muda para **suspenso**. Os detalhes do trabalho mostram o `RunHiveScript` status como **START_MANUAL**. Se você selecionar a ação, ela mostrará a seguinte mensagem de erro:

    JA002: User: oozie is not allowed to impersonate <USER>

**Motivo**: As configurações de permissão atuais não permitem que Oozie represente a conta de usuário especificada.

**Resolução**: Oozie pode representar usuários no grupo **usuários** . Use o `groups USERNAME` para ver os grupos dos quais a conta de usuário é membro. Se o usuário não for um membro do grupo **usuários** , use o seguinte comando para adicionar o usuário ao grupo:

    sudo adduser USERNAME users

> [!NOTE]  
> Pode levar vários minutos para que o HDInsight reconheça que o usuário foi adicionado ao grupo.

### <a name="launcher-error-sqoop"></a>ERRO do inicializador (Sqoop)

**Sintomas**: O status do trabalho muda para **eliminado**. Os detalhes do trabalho mostram o `RunSqoopExport` status como **erro**. Se você selecionar a ação, ela mostrará a seguinte mensagem de erro:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Motivo**: Sqoop não pode carregar o driver de banco de dados necessário para acessar o banco de dados.

**Resolução**: Ao usar o Sqoop de um trabalho do Oozie, você deve incluir o driver de banco de dados com outros recursos, como o Workflow. xml, que o trabalho usa. Além disso, referencie o arquivo que contém o driver de banco `<sqoop>...</sqoop>` de dados na seção do Workflow. xml.

Por exemplo, para o trabalho neste documento, você usaria as seguintes etapas:

1. Copie o `mssql-jdbc-7.0.0.jre8.jar` arquivo para o diretório **/Tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Modifique o `workflow.xml` para adicionar o seguinte XML em uma nova linha acima `</sqoop>`:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu como definir um fluxo de trabalho do Oozie e como executar uma tarefa do Oozie. Para saber mais sobre como trabalhar com o HDInsight, consulte os seguintes artigos:

* [Carregar dados para trabalhos de Apache Hadoop no HDInsight][hdinsight-upload-data]
* [Usar o Apache Sqoop com o Apache Hadoop no HDInsight][hdinsight-use-sqoop]
* [Usar Apache Hive com Apache Hadoop no HDInsight][hdinsight-use-hive]
* [Usar o Apache Pig com o Apache Hadoop no HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para o HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-preparation-output]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
