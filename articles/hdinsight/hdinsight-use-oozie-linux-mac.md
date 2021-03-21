---
title: Use fluxos de trabalho Hadoop Oozie em Azure HDInsight baseado em Linux
description: Use Hadoop Oozie em HDInsight baseado em Linux. Aprenda a definir um fluxo de trabalho Oozie e submeta um trabalho Oozie.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: b72d94c1cba3ea0476c6d44e68583f6668b167e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702781"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Utilizar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux

Aprenda a usar Apache Oozie com Apache Hadoop em Azure HDInsight. Oozie é um sistema de trabalho e coordenação que gere os empregos da Hadoop. Oozie está integrado com a pilha Hadoop, e apoia os seguintes trabalhos:

* Apache Hadoop MapReduce
* Porco-apache
* Colmeia Apache
* Apache Sqoop

Você também pode usar Oozie para agendar trabalhos específicos de um sistema, como programas java ou scripts de concha.

> [!NOTE]  
> Outra opção para definir fluxos de trabalho com HDInsight é usar a Azure Data Factory. Para saber mais sobre a Data Factory, consulte [Use Apache Pig and Apache Hive with Data Factory](../data-factory/transform-data.md). Para utilizar o Oozie em clusters com pacote de segurança empresarial, consulte [Run Apache Oozie em clusters HDInsight Hadoop com Pacote de Segurança Empresarial.](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop em HDInsight**. Ver [Começar com HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Um cliente SSH**. Consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Uma Base de Dados Azure SQL**.  Consulte [a Base de Dados Azure SQL no portal Azure](../azure-sql/database/single-database-create-quickstart.md).  Este artigo utiliza uma base de dados chamada **oozietest**.

* O esquema URI para o armazenamento primário dos seus clusters. `wasb://` para o Azure Storage, `abfs://` para Azure Data Lake Storage Gen2 ou `adl://` para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Azure, o URI será `wasbs://` . Consulte também, [transferência segura.](../storage/common/storage-require-secure-transfer.md)

## <a name="example-workflow"></a>Fluxo de trabalho exemplo

O fluxo de trabalho utilizado neste documento contém duas ações. As ações são definições para tarefas, tais como executar Hive, Sqoop, MapReduce, ou outros processos:

![Diagrama de fluxo de trabalho HDInsight oozie](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Uma ação da Hive executa um script HiveQL para extrair registos `hivesampletable` do que está incluído com HDInsight. Cada linha de dados descreve uma visita de um dispositivo móvel específico. O formato de gravação aparece como o seguinte texto:

    ```output
    8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
    23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
    23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1
    ```

    O script da Hive utilizado neste documento conta o total de visitas para cada plataforma, como Android ou iPhone, e armazena as contagens para uma nova tabela Hive.

    Para obter mais informações sobre a Colmeia, consulte [Use Apache Hive with HDInsight][hdinsight-use-hive].

2. Uma ação sqoop exporta o conteúdo da nova tabela Hive para uma tabela criada na Base de Dados Azure SQL. Para obter mais informações sobre o Sqoop, consulte [Use Apache Sqoop com HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md).

> [!NOTE]  
> Para versões Oozie suportadas em clusters HDInsight, veja [o que há de novo nas versões de cluster Hadoop fornecidas pela HDInsight](hdinsight-component-versioning.md).

## <a name="create-the-working-directory"></a>Criar o diretório de trabalho

Oozie espera que guarde todos os recursos necessários para um trabalho no mesmo diretório. Este exemplo utiliza `wasbs:///tutorials/useoozie`. Para criar este diretório, complete os seguintes passos:

1. Edite o código abaixo para substituir `sshuser` pelo nome de utilizador SSH para o cluster e `CLUSTERNAME` substitua-o pelo nome do cluster.  Em seguida, introduza o código para ligar ao cluster HDInsight [utilizando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Para criar o diretório, utilize o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > O `-p` parâmetro provoca a criação de todos os diretórios no caminho. O `data` diretório é utilizado para conter os dados utilizados pelo `useooziewf.hql` script.

3. Edite o código abaixo para substituir `sshuser` pelo seu nome de utilizador SSH.  Para se certificar de que o Oozie pode fazer-se passar pela sua conta de utilizador, utilize o seguinte comando:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Pode ignorar erros que indicam que o utilizador já é membro do `users` grupo.

## <a name="add-a-database-driver"></a>Adicione um controlador de base de dados

Este fluxo de trabalho utiliza a Sqoop para exportar dados para a base de dados SQL. Por isso, deve fornecer uma cópia do condutor JDBC usado para interagir com a base de dados SQL. Para copiar o controlador JDBC para o diretório de trabalho, utilize o seguinte comando da sessão SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Verifique o controlador JDBC que existe em `/usr/share/java/` .

Se o seu fluxo de trabalho utilizou outros recursos, como um frasco que contém uma aplicação MapReduce, também precisa de adicionar esses recursos.

## <a name="define-the-hive-query"></a>Defina a consulta de Colmeia

Use os seguintes passos para criar um script de linguagem de consulta de Colmeia (HiveQL) que define uma consulta. Você usará a consulta num fluxo de trabalho Oozie mais tarde neste documento.

1. A partir da ligação SSH, utilize o seguinte comando para criar um ficheiro chamado `useooziewf.hql` :

    ```bash
    nano useooziewf.hql
    ```

1. Após a abertura do editor nano GNU, utilize a seguinte consulta como o conteúdo do ficheiro:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Existem duas variáveis usadas no guião:

   * `${hiveTableName}`: Contém o nome da tabela a criar.

   * `${hiveDataFolder}`: Contém a localização para armazenar os ficheiros de dados para a tabela.

     O ficheiro de definição de fluxo de trabalho, workflow.xml neste artigo, transmite estes valores a este script HiveQL em tempo de execução.

1. Para guardar o ficheiro, selecione **Ctrl+X,** introduza **Y** e, em seguida, selecione **Enter**.  

1. Utilize o seguinte comando para copiar `useooziewf.hql` `wasbs:///tutorials/useoozie/useooziewf.hql` para:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Este comando armazena o `useooziewf.hql` ficheiro no armazenamento compatível com HDFS para o cluster.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

As definições de fluxo de trabalho Oozie são escritas na Hadoop Process Definition Language (hPDL), que é uma linguagem de definição de processo XML. Utilize os seguintes passos para definir o fluxo de trabalho:

1. Utilize a seguinte declaração para criar e editar um novo ficheiro:

    ```bash
    nano workflow.xml
    ```

2. Após a abertura do nano editor, insira o seguinte XML como o conteúdo do ficheiro:

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

    Existem duas ações definidas no fluxo de trabalho:

   * `RunHiveScript`: Esta ação é a ação inicial e executa o `useooziewf.hql` roteiro da Colmeia.

   * `RunSqoopExport`: Esta ação exporta os dados criados a partir do script da Colmeia para uma base de dados SQL utilizando o Sqoop. Esta ação só funciona se a `RunHiveScript` ação for bem sucedida.

     O fluxo de trabalho tem várias entradas, tais como `${jobTracker}` . Substituirá estas entradas pelos valores que utiliza na definição de trabalho. Criará a definição de emprego mais tarde neste documento.

     Note também a `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` entrada na secção Sqoop. Esta entrada instrui a Oozie a disponibilizar este arquivo para o Sqoop quando esta ação for executado.

3. Para guardar o ficheiro, selecione **Ctrl+X,** introduza **Y** e, em seguida, selecione **Enter**.  

4. Utilize o seguinte comando para copiar o `workflow.xml` ficheiro `/tutorials/useoozie/workflow.xml` para:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Criar uma tabela

> [!NOTE]  
> Existem muitas formas de ligar à Base de Dados SQL para criar uma tabela. Os passos seguintes utilizam [FreeTDS](https://www.freetds.org/) do cluster do HDInsight.

1. Utilize o seguinte comando para instalar FreeTDS no cluster HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Edite o código abaixo para substituir `<serverName>` pelo nome [lógico do servidor SQL](../azure-sql/database/logical-servers.md) e `<sqlLogin>` pelo login do servidor.  Introduza o comando para ligar à base de dados SQL pré-requisito.  Introduza a palavra-passe no pedido.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Recebe saída como o seguinte texto:

    ```output
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to oozietest
    1>
    ```

3. Na linha de comandos `1>`, introduza as seguintes linhas:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Estas declarações criam uma tabela, `mobiledata` chamada, que é usada pelo fluxo de trabalho.

    Para verificar se a tabela foi criada, utilize os seguintes comandos:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Vê a saída como o seguinte texto:

    ```output
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo             mobiledata      BASE TABLE
    ```

4. Saia do utilitário tsql entrando `exit` no `1>` pedido.

## <a name="create-the-job-definition"></a>Criar a definição de trabalho

A definição de trabalho descreve onde encontrar o workflow.xml. Também descreve onde encontrar outros ficheiros utilizados pelo fluxo de trabalho, tais como `useooziewf.hql` . Além disso, define os valores para as propriedades utilizadas dentro do fluxo de trabalho e dos ficheiros associados.

1. Para obter o endereço completo do armazenamento predefinido, utilize o seguinte comando. Este endereço é utilizado no ficheiro de configuração que cria no passo seguinte.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Este comando devolve informações como o seguinte XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Se o cluster HDInsight utilizar o Azure Storage como o armazenamento predefinido, o conteúdo do `<value>` elemento começa por `wasbs://` . Se o Azure Data Lake Storage Gen1 for utilizado, começa por `adl://` . Se o Azure Data Lake Storage Gen2 for utilizado, começa por `abfs://` .

    Guarde o conteúdo do `<value>` elemento, como é usado nos próximos passos.

2. Editar o xml abaixo da seguinte forma:

    |Valor reservado| Valor substituído|
    |---|---|
    |wasbs://mycontainer \@ mystorageaccount.blob.core.windows.net| Valor recebido do passo 1.|
    |administração| O seu nome de login para o cluster HDInsight se não for administrador.|
    |nome do servidor| Nome do servidor do servidor Azure SQL Database.|
    |sqlLogin| Início do servidor de base de dados Azure SQL.|
    |sqlPassword| Senha de login do servidor Azure SQL Database.|

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

    A maior parte das informações neste ficheiro é usada para preencher os valores utilizados nos ficheiros workflow.xml ou ooziewf.hql, tais como `${nameNode}` .  Se o caminho é um `wasbs` caminho, deve usar todo o caminho. Não o encurte `wasbs:///` para. A `oozie.wf.application.path` entrada define onde encontrar o ficheiro workflow.xml. Este ficheiro contém o fluxo de trabalho que foi gerido por este trabalho.

3. Para criar a configuração de definição de trabalho Oozie, utilize o seguinte comando:

    ```bash
    nano job.xml
    ```

4. Após a abertura do nano editor, cole o XML editado como o conteúdo do ficheiro.

5. Para guardar o ficheiro, selecione **Ctrl+X,** introduza **Y** e, em seguida, selecione **Enter**.

## <a name="submit-and-manage-the-job"></a>Submeter e gerir o trabalho

Os passos seguintes usam o comando Oozie para submeter e gerir fluxos de trabalho Oozie no cluster. O comando Oozie é uma interface amigável sobre a [API Oozie REST](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Quando utilizar o comando Oozie, deve utilizar o FQDN para o nó de cabeça HDInsight. Este FQDN só é acessível a partir do cluster, ou se o cluster estiver numa rede virtual Azure, a partir de outras máquinas na mesma rede.

1. Para obter o URL para o serviço Oozie, utilize o seguinte comando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Esta informação devolve informações como o seguinte XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` parte é a URL a usar com o comando Oozie.

2. Edite o código para substituir o URL pelo que recebeu anteriormente. Para criar uma variável ambiental para o URL, use o seguinte, para que não tenha que inseri-lo para cada comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Para submeter o trabalho, utilize o seguinte código:

    ```bash
    oozie job -config job.xml -submit
    ```

    Este comando carrega a informação do trabalho `job.xml` e submete-a ao Oozie, mas não a dirige.

    Após o fim do comando, deverá devolver a identificação do trabalho, por `0000005-150622124850154-oozie-oozi-W` exemplo, . Esta identificação é usada para gerir o trabalho.

4. Edite o código abaixo para substituir `<JOBID>` pelo ID devolvido no passo anterior.  Para visualizar o estado do trabalho, utilize o seguinte comando:

    ```bash
    oozie job -info <JOBID>
    ```

    Esta informação devolve informações como o seguinte texto:

    ```output
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
    ```

    Este trabalho tem um estatuto `PREP` de. Este estatuto indica que o trabalho foi criado, mas não começou.

5. Edite o código abaixo para substituir `<JOBID>` pelo ID devolvido anteriormente.  Para iniciar o trabalho, utilize o seguinte comando:

    ```bash
    oozie job -start <JOBID>
    ```

    Se verificar o estado após este comando, está em funcionamento, e a informação é devolvida para as ações dentro do trabalho.  O trabalho levará alguns minutos para ser concluído.

6. Edite o código abaixo para substituir `<serverName>` pelo nome do seu servidor e pelo login do `<sqlLogin>` servidor.  *Após o final da tarefa* com sucesso, pode verificar se os dados foram gerados e exportados para a tabela de base de dados SQL utilizando o seguinte comando.  Introduza a palavra-passe no pedido.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    No `1>` momento, insira a seguinte consulta:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    A informação devolvida é como o seguinte texto:

    ```output
    deviceplatform  count
    Android 31591
    iPhone OS       22731
    proprietary development 3
    RIM OS  3464
    Unknown 213
    Windows Phone   1791
    (6 rows affected)
    ```

Para obter mais informações sobre o comando Oozie, consulte [a ferramenta de linha de comando Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Com a API Oozie REST, pode construir as suas próprias ferramentas que funcionam com a Oozie. As seguintes informações específicas do HDInsight sobre a utilização da API Oozie REST:

* **URI**: Pode aceder à API REST a partir de fora do cluster em `https://CLUSTERNAME.azurehdinsight.net/oozie` .

* **Autenticação**: Para autenticar, utilize a conta API HTTP (administrador) e senha. Por exemplo:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Para obter mais informações sobre como utilizar a API Oozie REST, consulte [a Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie web UI

A UI web Oozie fornece uma visão web sobre o estado dos empregos de Oozie no cluster. Com a UI web pode ver as seguintes informações:

   * Estatuto do trabalho
   * Definição da tarefa
   * Configuração
   * Um gráfico das ações no trabalho
   * Registos para o trabalho

Você também pode ver os detalhes para as ações dentro de um trabalho.

Para aceder à UI web Oozie, complete os seguintes passos:

1. Crie um túnel SSH para o cluster HDInsight. Para obter mais informações, consulte [o Uso de Túneis SSH com HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Depois de criar um túnel, abra o UI web Ambari no seu navegador web usando URI `http://headnodehost:8080` .

3. Do lado esquerdo da página, selecione **Oozie**  >  **Quick Links**  >  **Oozie Web UI**.

    ![Apache Ambari oozie web ui passos](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. O UI web Oozie não apresenta os trabalhos de fluxo de trabalho em funcionamento. Para ver todos os trabalhos de fluxo de trabalho, selecione **All Jobs**.

    ![Trabalho de fluxo de trabalho da consola web Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Para ver mais informações sobre um trabalho, selecione o trabalho.

    ![Informações de trabalho de HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. A partir do separador **Job Info,** pode ver as informações básicas do trabalho e as ações individuais dentro do trabalho. Pode utilizar os separadores na parte superior para visualizar a Definição de **Emprego,** Configuração de **Emprego,** aceder ao Registo de **Emprego,** ou ver um gráfico acíclico direcionado (DAG) do trabalho no **âmbito do Job DAG**.

   * **Início de Trabalho**: Selecione o botão **Obter Registos** para obter todos os registos para a função, ou utilize o campo **'Filtro de pesquisa de entrada'** para filtrar os registos.

       ![Diário de trabalho de HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Job DAG**: O DAG é uma visão geral gráfica dos caminhos de dados percorridos através do fluxo de trabalho.

       !['HDInsight Apache Oozie job dag'](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Se selecionar uma das ações do separador **Job Info,** ela traz informações para a ação. Por exemplo, selecione a ação **RunSqoopExport.**

    ![Informações de ação de trabalho hdInsight oozie](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Pode ver detalhes para a ação, como um link para o **URL da consola.** Use este link para visualizar informações de rastreador de emprego para o trabalho.

## <a name="schedule-jobs"></a>Agendar empregos

Pode utilizar o coordenador para especificar um início, um fim e a frequência de ocorrência de empregos. Para definir um calendário para o fluxo de trabalho, complete os seguintes passos:

1. Utilize o seguinte comando para criar um ficheiro chamado **coordinator.xml:**

    ```bash
    nano coordinator.xml
    ```

    Utilize o seguinte XML como o conteúdo do ficheiro:

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
    > * `${coordFrequency}`: O tempo entre os casos de execução do trabalho.
    > * `${coordStart}`: A hora de início do trabalho.
    > * `${coordEnd}`: O fim do trabalho.
    > * `${coordTimezone}`: Os postos de trabalho dos coordenadores encontram-se num fuso horário fixo sem horário de verão, normalmente representado pela utilização da UTC. Este fuso horário é referido como o *fuso horário de processamento de Oozie.*
    > * `${wfPath}`: O caminho para a workflow.xml.

2. Para guardar o ficheiro, selecione **Ctrl+X,** introduza **Y** e, em seguida, selecione **Enter**.

3. Para copiar o ficheiro para o diretório de trabalho para este trabalho, utilize o seguinte comando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Para modificar o `job.xml` ficheiro que criou anteriormente, utilize o seguinte comando:

    ```bash
    nano job.xml
    ```

    Efetue as seguintes alterações:

   * Para instruir o Oozie a executar o ficheiro de coordenador em vez do fluxo de trabalho, mude `<name>oozie.wf.application.path</name>` para `<name>oozie.coord.application.path</name>` .

   * Para definir a `workflowPath` variável utilizada pelo coordenador, adicione o seguinte XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Substitua o `wasbs://mycontainer@mystorageaccount.blob.core.windows` texto pelo valor utilizado nas outras entradas no ficheiro job.xml.

   * Para definir o início, fim e frequência para o coordenador, adicione o seguinte XML:

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

       Estes valores definem a hora de início para as 12:00 horas de 10 de maio de 2018 e o fim para 12 de maio de 2018. O intervalo para executar este trabalho está definido para diariamente. A frequência é em minutos, então 24 horas x 60 minutos = 1440 minutos. Finalmente, o fuso horário está definido para UTC.

5. Para guardar o ficheiro, selecione **Ctrl+X,** introduza **Y** e, em seguida, selecione **Enter**.

6. Para submeter e iniciar o trabalho, utilize o seguinte comando:

    ```bash
    oozie job -config job.xml -run
    ```

7. Se for à UI web Oozie e selecionar o **separador Empregos Coordenadores,** vê informações como na seguinte imagem:

    ![Separador de empregos coordenadores de consola web Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    A **entrada da Próxima Materialização** contém a próxima vez que o trabalho for executado.

8. Tal como o trabalho anterior de fluxo de trabalho, se selecionar a entrada de emprego na UI web, exibe informações sobre o trabalho:

    ![Informações de trabalho coordenadoras de Apache Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Esta imagem só mostra execuções bem-sucedidas do trabalho, não as ações individuais dentro do fluxo de trabalho programado. Para ver as ações individuais, selecione uma das entradas **de Ação.**

    ![Separador de informações de trabalho de consola web OOzie](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a definir um fluxo de trabalho Oozie e como gerir um trabalho oozie. Para saber mais sobre como trabalhar com a HDInsight, consulte os seguintes artigos:

* [Upload de dados para empregos apache Hadoop em HDInsight](hdinsight-upload-data.md)
* [Use Apache Sqoop com Apache Hadoop em HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md)
* [Use a Colmeia Apache com Apache Hadoop em HDInsight](hadoop/hdinsight-use-hive.md)
* [Resolução de problemas do Apache Oozie](./troubleshoot-oozie.md)