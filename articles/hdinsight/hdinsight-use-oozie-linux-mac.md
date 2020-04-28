---
title: Utilize fluxos de trabalho Hadoop Oozie em Azure HDInsight baseado em Linux
description: Utilize Hadoop Oozie em HDInsight baseado em Linux. Aprenda a definir um fluxo de trabalho oozie e submeta um trabalho oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 93eddcd8ed0dae6ac6f010dce2e138fc018a06fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190661"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Utilizar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux

Aprenda a usar Apache Oozie com Apache Hadoop no Azure HDInsight. Oozie é um sistema de fluxo de trabalho e coordenação que gere os empregos de Hadoop. Oozie está integrado com a pilha hadoop, e apoia os seguintes trabalhos:

* Mapa do Hadoop ApacheReduzir
* Porco Apache
* Colmeia Apache
* Apache Sqoop

Você também pode usar Oozie para agendar trabalhos específicos para um sistema, como programas Java ou scripts de concha.

> [!NOTE]  
> Outra opção para definir fluxos de trabalho com hDInsight é usar a Azure Data Factory. Para saber mais sobre data factory, consulte [Use Apache Pig e Apache Hive com Data Factory](../data-factory/transform-data.md). Para utilizar o Oozie em clusters com pacote de segurança empresarial, consulte [run Apache Oozie em clusters Hadoop HDInsight com pacote](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)de segurança empresarial .

## <a name="prerequisites"></a>Pré-requisitos

* **Um aglomerado de Hadoop no HDInsight**. Ver [Começar com HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Um cliente SSH**. Consulte [a Ligação ao HDInsight (Apache Hadoop) utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Uma base de dados Azure SQL.**  Consulte [Criar uma base de dados Azure SQL no portal Azure](../sql-database/sql-database-get-started.md).  Este artigo utiliza uma base de dados chamada **oozietest**.

* O esquema URI para o armazenamento primário dos seus clusters. `wasb://`para armazenamento azure, `abfs://` para Azure Data `adl://` Lake Storage Gen2 ou para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para `wasbs://`o Armazenamento Azure, o URI seria . Ver também, [transferência segura.](../storage/common/storage-require-secure-transfer.md)

## <a name="example-workflow"></a>Fluxo de trabalho de exemplo

O fluxo de trabalho utilizado neste documento contém duas ações. As ações são definições para tarefas, tais como executar Hive, Sqoop, MapReduce ou outros processos:

![Diagrama de fluxo de trabalho HDInsight oozie](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Uma ação da Hive executa um script `hivesampletable` HiveQL para extrair registos do que está incluído com HDInsight. Cada linha de dados descreve uma visita de um dispositivo móvel específico. O formato de gravação aparece como o seguinte texto:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    O script da Hive usado neste documento conta o total de visitas para cada plataforma, como Android ou iPhone, e armazena as contagens para uma nova mesa da Hive.

    Para obter mais informações sobre a Hive, consulte [Use Apache Hive com HDInsight][hdinsight-use-hive].

2. Uma ação Sqoop exporta o conteúdo da nova tabela Hive para uma tabela criada na Base de Dados Azure SQL. Para mais informações sobre o Sqoop, consulte [Use Apache Sqoop com HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md).

> [!NOTE]  
> Para versões Oozie suportadas em clusters HDInsight, veja [as novidades nas versões de cluster Hadoop fornecidas pela HDInsight](hdinsight-component-versioning.md).

## <a name="create-the-working-directory"></a>Criar o diretório de trabalho

Oozie espera que guarde todos os recursos necessários para um trabalho no mesmo diretório. Este exemplo `wasbs:///tutorials/useoozie`utiliza. Para criar este diretório, complete os seguintes passos:

1. Editar o código `sshuser` abaixo para substituir pelo nome de utilizador `CLUSTERNAME` SSH para o cluster e substituir pelo nome do cluster.  Em seguida, introduza o código para se ligar ao cluster HDInsight [utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Para criar o diretório, utilize o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > O `-p` parâmetro provoca a criação de todos os diretórios no caminho. O `data` diretório é usado para conter `useooziewf.hql` os dados utilizados pelo script.

3. Edite o código `sshuser` abaixo para substituir pelo seu nome de utilizador SSH.  Para se certificar de que a Oozie pode fazer-se passar pela sua conta de utilizador, utilize o seguinte comando:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Pode ignorar erros que indicam que o `users` utilizador já é membro do grupo.

## <a name="add-a-database-driver"></a>Adicione um controlador de base de dados

Este fluxo de trabalho utiliza o SQoop para exportar dados para a base de dados SQL. Por isso, deve fornecer uma cópia do controlador JDBC utilizado para interagir com a base de dados SQL. Para copiar o condutor JDBC para o diretório de trabalho, utilize o seguinte comando da sessão SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Verifique o condutor real da `/usr/share/java/`JDBC que existe em .

Se o seu fluxo de trabalho usou outros recursos, como um frasco que contém uma aplicação MapReduce, também precisa de adicionar esses recursos.

## <a name="define-the-hive-query"></a>Defina a consulta da Colmeia

Use os seguintes passos para criar um script de consulta da Hive (HiveQL) que define uma consulta. Você usará a consulta num fluxo de trabalho de Oozie mais tarde neste documento.

1. A partir da ligação SSH, utilize `useooziewf.hql`o seguinte comando para criar um ficheiro chamado:

    ```bash
    nano useooziewf.hql
    ```

1. Depois da abertura do editor da GNU Nano, utilize a seguinte consulta como conteúdo do ficheiro:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Existem duas variáveis usadas no script:

   * `${hiveTableName}`: Contém o nome da tabela a criar.

   * `${hiveDataFolder}`: Contém a localização para armazenar os ficheiros de dados para a tabela.

     O ficheiro de definição de fluxo de trabalho, workflow.xml neste artigo, passa estes valores para este script HiveQL em tempo de execução.

1. Para guardar o ficheiro, selecione **Ctrl+X,** **introduza Y,** e, em seguida, selecione **Enter**.  

1. Utilize o seguinte `useooziewf.hql` comando `wasbs:///tutorials/useoozie/useooziewf.hql`para copiar para:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Este comando `useooziewf.hql` armazena o ficheiro no armazenamento compatível com HDFS para o cluster.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

As definições de fluxo de trabalho oozie são escritas na Linguagem de Definição de Processo hadoop (hPDL), que é uma linguagem de definição de processo XML. Utilize os seguintes passos para definir o fluxo de trabalho:

1. Utilize a seguinte declaração para criar e editar um novo ficheiro:

    ```bash
    nano workflow.xml
    ```

2. Depois de o editor nano abrir, introduza o seguinte XML como conteúdo do ficheiro:

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

   * `RunHiveScript`: Esta ação é a `useooziewf.hql` ação de início e executa o script da Colmeia.

   * `RunSqoopExport`: Esta ação exporta os dados criados do script da Hive para uma base de dados SQL utilizando o Sqoop. Esta ação só `RunHiveScript` funciona se a ação for bem sucedida.

     O fluxo de trabalho tem `${jobTracker}`várias entradas, tais como . Substituirá estas entradas com os valores que utiliza na definição de trabalho. Criará a definição de emprego mais tarde neste documento.

     Note também `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` a entrada na secção Sqoop. Esta entrada instrui a Oozie a disponibilizar este arquivo para o Sqoop quando esta ação for executado.

3. Para guardar o ficheiro, selecione **Ctrl+X,** **introduza Y,** e, em seguida, selecione **Enter**.  

4. Utilize o seguinte comando `workflow.xml` para `/tutorials/useoozie/workflow.xml`copiar o ficheiro para:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Criar uma tabela

> [!NOTE]  
> Existem muitas formas de se ligar à Base de Dados SQL para criar uma tabela. Os passos seguintes utilizam [FreeTDS](https://www.freetds.org/) do cluster do HDInsight.

1. Utilize o seguinte comando para instalar o FreeTDS no cluster HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Edite o código `<serverName>` abaixo para substituir pelo nome `<sqlLogin>` do servidor Azure SQL e com o login do servidor Azure SQL.  Introduza o comando para ligar à base de dados SQL pré-requisito.  Introduza a palavra-passe no momento.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Recebe saída como o seguinte texto:

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

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Estas declarações criam `mobiledata`uma tabela, chamada , que é usada pelo fluxo de trabalho.

    Para verificar se a tabela foi criada, utilize os seguintes comandos:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Vê a saída como o seguinte texto:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Saia da utilidade tsql `exit` entrando no `1>` aviso.

## <a name="create-the-job-definition"></a>Criar a definição de trabalho

A definição de trabalho descreve onde encontrar o fluxo de trabalho.xml. Também descreve onde encontrar outros ficheiros utilizados `useooziewf.hql`pelo fluxo de trabalho, tais como . Além disso, define os valores das propriedades utilizadas dentro do fluxo de trabalho e dos ficheiros associados.

1. Para obter o endereço completo do armazenamento predefinido, utilize o seguinte comando. Este endereço é utilizado no ficheiro de configuração que cria no próximo passo.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Este comando devolve informações como o seguinte XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Se o cluster HDInsight utilizar o Armazenamento `<value>` Azure como `wasbs://`armazenamento predefinido, o conteúdo do elemento começa com . Se o Azure Data Lake Storage Gen1 `adl://`for utilizado, começa com . Se o Azure Data Lake Storage Gen2 for utilizado, começa com `abfs://`.

    Guarde o `<value>` conteúdo do elemento, pois é usado nos próximos passos.

2. Editar o xml abaixo da seguinte forma:

    |Valor do espaço reservado| Valor substituído|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Valor recebido do passo 1.|
    |admin| O seu nome de login para o cluster HDInsight, se não administrador.|
    |nome do servidor| Nome do servidor de base de dados Azure SQL.|
    |sqlLogin| Login no servidor de base de dados Azure SQL.|
    |sqlPassword| Palavra-passe de login do servidor de base de dados Azure SQL.|

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

    A maior parte das informações neste ficheiro são utilizadas para preencher os valores utilizados nos `${nameNode}`ficheiros workflow.xml ou ooziewf.hql, tais como .  Se o caminho `wasbs` é um caminho, deve usar o caminho completo. Não encurte para `wasbs:///`apenas. A `oozie.wf.application.path` entrada define onde encontrar o ficheiro workflow.xml. Este ficheiro contém o fluxo de trabalho que foi gerido por este trabalho.

3. Para criar a configuração da definição de definição de trabalho oozie, use o seguinte comando:

    ```bash
    nano job.xml
    ```

4. Depois da abertura do editor nano, cola o XML editado como o conteúdo do ficheiro.

5. Para guardar o ficheiro, selecione **Ctrl+X,** **introduza Y,** e, em seguida, selecione **Enter**.

## <a name="submit-and-manage-the-job"></a>Submeter e gerir o trabalho

Os seguintes passos utilizam o comando Oozie para submeter e gerir os fluxos de trabalho da Oozie no cluster. O comando Oozie é uma interface amigável sobre a [API Rest Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Quando utilizar o comando Oozie, deve utilizar o FQDN para o nó de cabeça HDInsight. Este FQDN só é acessível a partir do cluster, ou se o cluster estiver numa rede virtual Azure, a partir de outras máquinas na mesma rede.

1. Para obter o URL ao serviço Oozie, utilize o seguinte comando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Isto devolve informações como o seguinte XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    A `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` porção é o URL a utilizar com o comando Oozie.

2. Editar o código para substituir o URL pelo que recebeu anteriormente. Para criar uma variável ambiental para o URL, use o seguinte, para que não tenha que inseri-lo para cada comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Para submeter o trabalho, utilize o seguinte código:

    ```bash
    oozie job -config job.xml -submit
    ```

    Este comando carrega a `job.xml` informação de trabalho e submete-a a Oozie, mas não a executa.

    Após o fim do comando, deve devolver a identificação do trabalho, por exemplo, `0000005-150622124850154-oozie-oozi-W`. Esta identificação é usada para gerir o trabalho.

4. Editar o código `<JOBID>` abaixo para substituir pelo ID devolvido no passo anterior.  Para ver o estado do trabalho, utilize o seguinte comando:

    ```bash
    oozie job -info <JOBID>
    ```

    Isto devolve informações como o seguinte texto:

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

    Este trabalho tem `PREP`um estatuto de . Este estado indica que o trabalho foi criado, mas não começou.

5. Editar o código `<JOBID>` abaixo para substituir o ID devolvido anteriormente.  Para iniciar o trabalho, utilize o seguinte comando:

    ```bash
    oozie job -start <JOBID>
    ```

    Se verificar o estado após este comando, está em estado de execução, e a informação é devolvida para as ações dentro do trabalho.  O trabalho levará alguns minutos para ser concluído.

6. Edite o código `<serverName>` abaixo para substituir pelo nome `<sqlLogin>` do servidor Azure SQL e com o login do servidor Azure SQL.  *Após o acabamento da tarefa* com sucesso, pode verificar se os dados foram gerados e exportados para a tabela de bases de dados SQL utilizando o seguinte comando.  Introduza a palavra-passe no momento.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    No `1>` momento, insira a seguinte consulta:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    A informação devolvida é como o seguinte texto:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para obter mais informações sobre o comando Oozie, consulte a [ferramenta de linha de comando Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Com a API Rest Oozie, você pode construir suas próprias ferramentas que trabalham com Oozie. As seguintes informações específicas do HDInsight sobre a utilização da API Rest Oozie:

* **URI**: Pode aceder à API REST `https://CLUSTERNAME.azurehdinsight.net/oozie`de fora do cluster a .

* **Autenticação**: Para autenticar, utilize a Conta API do cluster HTTP (administrador) e a palavra-passe. Por exemplo:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Para obter mais informações sobre como utilizar a API Oozie REST, consulte [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie web UI

A Oozie web UI fornece uma visão baseada na web sobre o estado dos empregos oozie no cluster. Com a Web UI pode ver as seguintes informações:

   * Estatuto laboral
   * Definição da tarefa
   * Configuração
   * Um gráfico das ações no trabalho
   * Registos para o trabalho

Pode também ver os detalhes das ações dentro de um trabalho.

Para aceder à UI web oozie, complete os seguintes passos:

1. Crie um túnel SSH para o cluster HDInsight. Para mais informações, consulte [Utilize o Túnel SSH com hDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Depois de criar um túnel, abra o UI web `http://headnodehost:8080`ambari no seu navegador web usando URI .

3. Do lado esquerdo da página, selecione **Oozie** > **Quick Links** > **Oozie Web UI**.

    ![Apache Ambari oozie web ui passos](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. A UI web oozie falha para exibir os trabalhos de fluxo de trabalho em execução. Para ver todos os trabalhos de fluxo de trabalho, selecione **All Jobs**.

    ![Trabalhos de fluxo de trabalho de consola web Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Para ver mais informações sobre um trabalho, selecione o trabalho.

    ![HDInsight Apache Oozie informação de trabalho](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. A partir do separador **Job Info,** pode ver as informações básicas sobre o trabalho e as ações individuais dentro do trabalho. Pode utilizar os separadores na parte superior para visualizar a Definição de **Trabalho,** **Configuração de Trabalho,** aceder ao **Registo de Trabalho,** ou ver um gráfico acíclico direcionado (DAG) do trabalho no âmbito do **Job DAG**.

   * **Início de trabalho**: Selecione o botão **'Obter Registos'** para obter todos os registos para o trabalho ou utilize o campo **'Filtro de Procura de Entrar'** para filtrar os registos.

       ![HDInsight Apache Oozie log de trabalho](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Job DAG**: O DAG é uma visão gráfica dos caminhos de dados tomados através do fluxo de trabalho.

       !['HDInsight Apache Oozie job dag'](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Se selecionar uma das ações do separador **Job Info,** ele traz informações para a ação. Por exemplo, selecione a ação **RunSqoopExport.**

    ![HDInsight oozie informação de ação de trabalho](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Pode ver detalhes para a ação, como uma ligação ao URL da **Consola**. Utilize este link para visualizar informações sobre rastreadorde emprego para o trabalho.

## <a name="schedule-jobs"></a>Agendar empregos

Pode utilizar o coordenador para especificar um início, um fim e a frequência de ocorrência para empregos. Para definir um horário para o fluxo de trabalho, complete os seguintes passos:

1. Utilize o seguinte comando para criar um ficheiro chamado **coordenador.xml:**

    ```bash
    nano coordinator.xml
    ```

    Utilize o seguinte XML como conteúdo do ficheiro:

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
    > As `${...}` variáveis são substituídas por valores na definição de emprego em tempo de execução. As variáveis são:
    >
    > * `${coordFrequency}`: O tempo entre as instâncias de funcionamento do trabalho.
    > * `${coordStart}`: A hora de início do trabalho.
    > * `${coordEnd}`: O fim do trabalho.
    > * `${coordTimezone}`: Os postos de trabalho dos coordenadores encontram-se num fuso horário fixo sem horário de verão, normalmente representado pela utilização da UTC. Este fuso horário é referido como o fuso horário de processamento de *Oozie.*
    > * `${wfPath}`: O caminho para o fluxo de trabalho.xml.

2. Para guardar o ficheiro, selecione **Ctrl+X,** **introduza Y,** e, em seguida, selecione **Enter**.

3. Para copiar o ficheiro para o diretório de trabalho para este trabalho, utilize o seguinte comando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Para modificar `job.xml` o ficheiro que criou anteriormente, utilize o seguinte comando:

    ```bash
    nano job.xml
    ```

    Efetue as seguintes alterações:

   * Para instruir o Oozie a executar o `<name>oozie.wf.application.path</name>` ficheiro `<name>oozie.coord.application.path</name>`coordenador em vez do fluxo de trabalho, mude para .

   * Para definir `workflowPath` a variável utilizada pelo coordenador, adicione o seguinte XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Substitua `wasbs://mycontainer@mystorageaccount.blob.core.windows` o texto pelo valor utilizado nas outras entradas no ficheiro job.xml.

   * Para definir o início, a extremidade e a frequência para o coordenador, adicione o seguinte XML:

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

       Estes valores fixam a hora de início para as 12:00 de 10 de maio de 2018, e o fim do dia 12 de maio de 2018. O intervalo para executar este trabalho está definido para o dia-a-dia. A frequência é em minutos, então 24 horas x 60 minutos = 1440 minutos. Finalmente, o fuso horário está definido para UTC.

5. Para guardar o ficheiro, selecione **Ctrl+X,** **introduza Y,** e, em seguida, selecione **Enter**.

6. Para submeter e iniciar o trabalho, utilize o seguinte comando:

    ```bash
    oozie job -config job.xml -run
    ```

7. Se for à UI web da Oozie e selecionar o separador **Empregos Coordenador,** verá informações como na seguinte imagem:

    ![Oozie web console coordenador jobs tab](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    A entrada seguinte da **Materialização** contém a próxima vez que o trabalho for executado.

8. Tal como o trabalho anterior de fluxo de trabalho, se selecionar a entrada de emprego na UI web, exibe informações sobre o trabalho:

    ![Apache Oozie coordenador informação de trabalho](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Esta imagem só mostra o sucesso do trabalho, não as ações individuais dentro do fluxo de trabalho programado. Para ver as ações individuais, selecione uma das entradas **de Ação.**

    ![Separador de informação sobre trabalho na consola web OOzie](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Resolução de problemas

Com a Oozie UI, pode ver os registos da Oozie. O Oozie UI também contém links para os registos JobTracker para as tarefas MapReduce que foram iniciadas pelo fluxo de trabalho. O padrão para resolução de problemas deve ser:

   1. Veja o trabalho na Oozie web UI.

   2. Se houver um erro ou falha para uma ação específica, selecione a ação para ver se o campo **Error Message** fornece mais informações sobre a falha.

   3. Se disponível, utilize o URL da ação para ver mais detalhes, como os registos JobTracker, para a ação.

Seguem-se erros específicos que poderá encontrar e como resolvê-los.

### <a name="ja009-cant-initialize-cluster"></a>JA009: Não pode inicializar o cluster

**Sintomas**: O estado do trabalho muda para **SUSPENSO**. Os detalhes para `RunHiveScript` o trabalho mostram o estado como **START_MANUAL**. A seleção da ação apresenta a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: Os endereços de armazenamento do Azure Blob utilizados no ficheiro **job.xml** não contêm o recipiente de armazenamento ou o nome da conta de armazenamento. O formato de endereço `wasbs://containername@storageaccountname.blob.core.windows.net`de armazenamento Blob deve ser .

**Resolução**: Alterar os endereços de armazenamento blob que o trabalho utiliza.

### <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie não está autorizado &lt;a personificar USER&gt;

**Sintomas**: O estado do trabalho muda para **SUSPENSO**. Os detalhes para `RunHiveScript` o trabalho mostram o estado como **START_MANUAL**. Se selecionar a ação, mostra a seguinte mensagem de erro:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: As definições de permissão atuais não permitem que o Oozie se personifique pela conta de utilizador especificada.

**Resolução**: Oozie pode **`users`** personificar utilizadores no grupo. Utilize `groups USERNAME` o para ver os grupos de que a conta de utilizador é membro. Se o utilizador não for **`users`** membro do grupo, utilize o seguinte comando para adicionar o utilizador ao grupo:

    sudo adduser USERNAME users

> [!NOTE]  
> Pode levar alguns minutos até que o HDInsight reconheça que o utilizador foi adicionado ao grupo.

### <a name="launcher-error-sqoop"></a>ERRO do lançador (Sqoop)

**Sintomas**: O estado do trabalho muda para **KILLED**. Os detalhes para `RunSqoopExport` o trabalho mostram o estado como **ERRO**. Se selecionar a ação, mostra a seguinte mensagem de erro:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: O Sqoop não consegue carregar o controlador de base de dados necessário para aceder à base de dados.

**Resolução**: Quando utilizar o Sqoop de um trabalho da Oozie, deve incluir o condutor da base de dados com os outros recursos, como o workflow.xml, que o trabalho utiliza. Consulte também o arquivo que contém `<sqoop>...</sqoop>` o controlador de base de dados da secção do fluxo de trabalho.xml.

Por exemplo, para o trabalho neste documento, utilizaria os seguintes passos:

1. Copie `mssql-jdbc-7.0.0.jre8.jar` o ficheiro para o diretório **/tutorial/useoozie:**

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Modifique `workflow.xml` o seguinte XML numa nova `</sqoop>`linha acima:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a definir um fluxo de trabalho oozie e como gerir um trabalho de Oozie. Para saber mais sobre como trabalhar com o HDInsight, consulte os seguintes artigos:

* [Enviar dados para empregos apache Hadoop no HDInsight](hdinsight-upload-data.md)
* [Use Apache Sqoop com Apache Hadoop em HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md)
* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hadoop/hdinsight-use-hive.md)
