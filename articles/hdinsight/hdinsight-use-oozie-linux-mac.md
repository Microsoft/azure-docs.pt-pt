---
title: Utilizar fluxos de trabalho do Oozie do Hadoop no HDInsight do Azure baseado em Linux
description: Utilize Oozie do Hadoop no HDInsight baseado em Linux. Saiba como definir um fluxo de trabalho do Oozie e submeter uma tarefa de Oozie.
services: hdinsight
ms.service: hdinsight
ms.custom: hdinsightactive
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: dfbf9a3a9b800fec5df4cf527ddd4ec8e3f55b37
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853244"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Utilizar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight do Azure baseado em Linux

Saiba como utilizar o Apache Oozie com o Apache Hadoop no HDInsight do Azure. Oozie é um sistema de fluxo de trabalho e a coordenação que gere as tarefas do Hadoop. Oozie está integrado com a pilha do Hadoop e suporta as seguintes tarefas:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Também pode utilizar o Oozie para agendar tarefas que são específicas para um sistema, como programas de Java ou scripts de shell.

> [!NOTE]  
> Outra opção para definir fluxos de trabalho com o HDInsight é utilizar o Azure Data Factory. Para saber mais sobre o Data Factory, veja [utilizar o Apache Pig e Apache Hive com o Data Factory][azure-data-factory-pig-hive]. Utilizar Oozie em clusters com o Enterprise Security Package, veja [executar o Apache Oozie no Hadoop do HDInsight clusters com o Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do Hadoop no HDInsight**. Ver [introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Um cliente SSH**. Ver [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Uma base de dados SQL do Azure**.  Ver [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started.md).  Este artigo utiliza uma base de dados com o nome `oozietest`.

* **Possíveis alterações à configuração de armazenamento.**  Ver [configuração de armazenamento](#storage-configuration) se utilizar o tipo de conta de armazenamento `BlobStorage`.

## <a name="storage-configuration"></a>Configuração do armazenamento
É necessária nenhuma ação se a conta de armazenamento utilizada é do tipo `Storage (general purpose v1)` ou `StorageV2 (general purpose v2)`.  O processo no artigo produzirá um resultado para, pelo menos, `/mapreducestaging`.  Irá conter uma configuração predefinida do hadoop `/mapreducestaging` no `fs.azure.page.blob.dir` variável de configuração na `core-site.xml` para o serviço `HDFS`.  Esta configuração fará com que a saída para o diretório para blobs de páginas, que não é suportado para tipo de conta de armazenamento `BlobStorage`.  Para utilizar `BlobStorage` neste artigo, remova `/mapreducestaging` partir o `fs.azure.page.blob.dir` variável de configuração.  A configuração pode ser acedida a partir da [IU do Ambari](hdinsight-hadoop-manage-ambari.md).  Caso contrário, receberá a mensagem de erro: `Page blob is not supported for this account type.`

> [!NOTE]  
> A conta de armazenamento utilizada neste artigo possui [transferência segura](../storage/common/storage-require-secure-transfer.md) ativado e, portanto, `wasbs` vez `wasb` é usado em todo o artigo.

## <a name="example-workflow"></a>Fluxo de trabalho de exemplo

O fluxo de trabalho utilizado neste documento contém duas ações. Ações são as definições para tarefas, como a execução do Hive, Sqoop, MapReduce ou outros processos:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

1. Uma ação de Hive executa um script de HiveQL para extrair os registos a partir do `hivesampletable` que está incluído no HDInsight. Cada linha de dados descreve uma visita de um dispositivo móvel específico. O formato de registo aparece como o seguinte texto:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    O script de ramo de registo utilizado neste documento conta o total de visitas para cada plataforma, como Android ou iPhone e armazena as contagens para uma nova tabela do Hive.

    Para obter mais informações sobre o Hive, consulte [utilizar o Apache Hive com HDInsight][hdinsight-use-hive].

2. Uma ação de Sqoop exporta o conteúdo da nova tabela do Hive para uma tabela criada na base de dados do Azure SQL. Para obter mais informações sobre o Sqoop, consulte [utilizar o Apache Sqoop com o HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Para versões suportadas do Oozie em clusters do HDInsight, consulte [quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Criar o diretório de trabalho

Oozie espera que armazene todos os recursos necessários para uma tarefa no mesmo diretório. Este exemplo utiliza `wasbs:///tutorials/useoozie`. Para criar este diretório, conclua os seguintes passos:

1. Editar o código abaixo para substituir `sshuser` com o SSH utilizador atribua um nome para o cluster e substitua `clustername` com o nome do cluster.  Em seguida, introduza o código para ligar ao cluster HDInsight pela [através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. Para criar o diretório, utilize o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > O `-p` parâmetro faz com que a criação de todos os diretórios no caminho. O `data` directory é utilizado para armazenar os dados utilizados pelo `useooziewf.hql` script.

3. Editar o código abaixo para substituir `username` com seu nome de utilizador SSH.  Para certificar-se de que o Oozie pode representar a conta de utilizador, utilize o seguinte comando:

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > Pode ignorar os erros que indicam que o utilizador já é membro do `users` grupo.

## <a name="add-a-database-driver"></a>Adicionar um driver de base de dados

Uma vez que este fluxo de trabalho utiliza o Sqoop para exportar dados para a base de dados SQL, tem de fornecer uma cópia do driver JDBC utilizado para interagir com a base de dados SQL. Para copiar o JDBC driver para o diretório de trabalho, utilize o seguinte comando a partir da sessão SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Verifique se o controlador JDBC real que existe em `/usr/share/java/`.

Se o seu fluxo de trabalho utilizados outros recursos, tais como um jar que contém uma aplicação de MapReduce, terá de adicionar esses recursos também.

## <a name="define-the-hive-query"></a>Definir a consulta do Hive

Utilize os seguintes passos para criar um script de idioma (HiveQL) de consulta do Hive que define uma consulta. Irá utilizar a consulta num fluxo de trabalho do Oozie mais tarde neste documento.

1. A partir da ligação de SSH, utilize o seguinte comando para criar um arquivo chamado `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Depois de abre o editor do GNU nano, utilize a seguinte consulta como o conteúdo do ficheiro:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Existem duas variáveis no script:

   * `${hiveTableName}`: Contém o nome da tabela a ser criada.

   * `${hiveDataFolder}`: Contém a localização para armazenar os ficheiros de dados para a tabela.

     O ficheiro de definição de fluxo de trabalho, workflow.xml neste tutorial, passa esses valores para este script de HiveQL no tempo de execução.

4. Para guardar o ficheiro, selecione Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.  

5. Utilize o comando seguinte para copiar `useooziewf.hql` para `wasbs:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Este comando armazena os `useooziewf.hql` ficheiro no armazenamento compatível com HDFS do cluster.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

Definições de fluxo de trabalho de Oozie são escritas no Hadoop processo de definição de idioma (hPDL), que é uma linguagem de definição do processo XML. Utilize os seguintes passos para definir o fluxo de trabalho:

1. Utilize a seguinte instrução para criar e editar um novo ficheiro:

    ```bash
    nano workflow.xml
    ```

2. Depois de abre o editor nano, introduza o seguinte XML como o conteúdo do ficheiro:

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

   * `RunHiveScript`: Esta ação é a ação de início e executa o `useooziewf.hql` script de ramo de registo.

   * `RunSqoopExport`: Esta ação exporta os dados criados do script do Hive para uma base de dados SQL com o Sqoop. Esta ação é executada apenas se o `RunHiveScript` ação é efetuada com êxito.

     O fluxo de trabalho tem várias entradas, como `${jobTracker}`. Substituirá essas entradas com os valores que utilizar na definição de tarefa. Irá criar a definição de tarefa mais tarde neste documento.

     Tenha também em atenção o `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` entrada na secção Sqoop. Essa entrada instrui o Oozie para disponibilizar este arquivo para Sqoop ao executar esta ação.

3. Para guardar o ficheiro, selecione Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.  

4. Utilize o comando seguinte para copiar o `workflow.xml` de ficheiros para `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Criar uma tabela

> [!NOTE]  
> Existem várias formas de ligar à base de dados SQL para criar uma tabela. Os passos seguintes utilizam [FreeTDS](http://www.freetds.org/) do cluster do HDInsight.

1. Utilize o seguinte comando para instalar o FreeTDS no cluster do HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Editar o código abaixo para substituir `<serverName>` com o nome do servidor SQL do Azure, e `<sqlLogin>` com o início de sessão do servidor SQL do Azure.  Introduza o comando para ligar para o pré-requisito de base de dados SQL.  Introduza a palavra-passe na linha de comandos.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    É apresentado o resultado como o seguinte texto:

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

    Quando for introduza a declaração `GO`, as instruções anteriores são avaliadas. Essas instruções criar uma tabela, denominada `mobiledata`, que é utilizado pelo fluxo de trabalho.

    Para verificar que a tabela foi criada, utilize os seguintes comandos:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Veja o resultado como o seguinte texto:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Saia do utilitário de tsql ao introduzir `exit` no `1>` prompt.

## <a name="create-the-job-definition"></a>Criar a definição de tarefa

A definição de tarefa descreve onde encontrar o workflow.xml. Também descreve onde encontrar outros arquivos usados pelo fluxo de trabalho, tais como `useooziewf.hql`. Além disso, ele define os valores para as propriedades utilizadas no fluxo de trabalho e os ficheiros associados.

1. Para obter o endereço completo do armazenamento predefinido, utilize o seguinte comando. Este endereço é utilizado no ficheiro de configuração que criar no próximo passo.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Este comando devolve informações como o seguinte XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Se o cluster de HDInsight utiliza o armazenamento do Azure como armazenamento predefinido, o `<value>` conteúdo de elemento de começar por `wasbs://`. Se a geração 1 de armazenamento do Azure Data Lake é utilizado em vez disso, ele começa com `adl://`. Se for utilizada a geração 2 de armazenamento do Azure Data Lake, ele começa com `abfs://`.

    Salvar o conteúdo do `<value>` elemento, como ele é usado nos passos seguintes.

2. Edite o xml abaixo da seguinte forma:

    |Valor do marcador de posição| Valor substituído|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Valor recebido do passo 1.|
    |admin| O nome de início de sessão para o cluster de HDInsight se não for administrador.|
    |ServerName| Nome de servidor do banco de dados SQL do Azure.|
    |sqlLogin| Logon de servidor do banco de dados SQL do Azure.|
    |sqlPassword| O Azure SQL database server início de sessão palavra-passe.|

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

    A maioria das informações existentes neste ficheiro é utilizado para preencher os valores utilizados nos ficheiros de workflow.xml ou ooziewf.hql, como `${nameNode}`.  Se o caminho é um `wasbs` caminho, tem de utilizar o caminho completo. Não Encurte-a para apenas `wasbs:///`. O `oozie.wf.application.path` entrada define onde encontrar o ficheiro de workflow.xml. Este ficheiro contém o fluxo de trabalho que foi executado por esta tarefa.

3. Para criar a configuração de definição de tarefa do Oozie, utilize o seguinte comando:

    ```bash
    nano job.xml
    ```

4. Depois de abre o editor nano, cole o XML editado como o conteúdo do ficheiro.

5. Para guardar o ficheiro, selecione Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.

## <a name="submit-and-manage-the-job"></a>Submeter e gerir a tarefa

Os seguintes passos utilizam o comando de Oozie para submeter e gerir fluxos de trabalho de Oozie no cluster. O comando de Oozie é uma interface amigável ao longo da [API do REST de Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Quando utiliza o comando de Oozie, tem de utilizar o FQDN para o nó principal do HDInsight. Este FQDN só é acessível a partir do cluster, ou se o cluster está numa rede virtual do Azure, a partir de outras máquinas na mesma rede.

1. Para obter o URL para o serviço de Oozie, utilize o seguinte comando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Esta ação devolve informações como o seguinte XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    O `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` parte é o URL a utilizar com o comando de Oozie.

2. Edite o código para substituir o URL com aquele que recebeu anteriormente. Para criar uma variável de ambiente para o URL, utilize o seguinte, para que não tenha de introduzi-los de cada comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Para submeter a tarefa, utilize o seguinte:

    ```bash
    oozie job -config job.xml -submit
    ```

    Este comando carrega as informações de tarefa de `job.xml` e envia para Oozie, mas não é executado.

    Quando o comando estiver concluído, deverá devolver o ID da tarefa, por exemplo, `0000005-150622124850154-oozie-oozi-W`. Este ID é utilizado para gerir a tarefa.

4. Editar o código abaixo para substituir `<JOBID>` com a identificação retornada no passo anterior.  Para ver o estado da tarefa, utilize o seguinte comando:

    ```bash
    oozie job -info <JOBID>
    ```

    Esta ação devolve informações como o seguinte texto:

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

    Esta tarefa tem o estado `PREP`. Este status indica que a tarefa foi criada, mas não foi iniciada.

5. Editar o código abaixo para substituir `<JOBID>` com a identificação retornada anteriormente.  Para iniciar a tarefa, utilize o seguinte comando:

    ```bash
    oozie job -start JOBID
    ```

    Se verificar o estado após este comando, está no estado de execução e informações são devolvidas para as ações dentro da tarefa.  O trabalho demora alguns minutos a concluir.

6. Editar o código abaixo para substituir `<serverName>` com o nome do servidor SQL do Azure, e `<sqlLogin>` com o início de sessão do servidor SQL do Azure.  Depois da tarefa foi concluída com êxito, pode verificar que os dados foi gerados e exportados para a tabela de base de dados SQL com o comando seguinte.  Introduza a palavra-passe na linha de comandos.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Na `1>` , introduza a seguinte consulta:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    As informações devolvidas são como o seguinte texto:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para obter mais informações sobre o comando de Oozie, consulte [ferramenta da linha de comandos do Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API de REST de Oozie

Com a API de REST do Oozie, pode criar suas próprias ferramentas que funcionam com o Oozie. Segue-se informações específicas do HDInsight sobre a utilização da API de REST do Oozie:

* **URI**: Pode acessar a API de REST do fora do cluster em `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autenticação**: Para se autenticar, use a API, a conta HTTP de cluster (admin) e a palavra-passe. Por exemplo:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Para obter mais informações sobre como utilizar a API de REST do Oozie, consulte [API de serviços Web do Apache Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>IU da web do Oozie

A IU da web de Oozie fornece uma vista baseada na web para o estado das tarefas de Oozie no cluster. Com a IU da web pode ver as seguintes informações:

   * Estado da tarefa
   * Definição da tarefa
   * Configuração
   * Um gráfico das ações da tarefa
   * Registos da tarefa

Também pode ver os detalhes para as ações dentro de uma tarefa.

Para aceder à IU da web de Oozie, conclua os seguintes passos:

1. Crie um túnel SSH para o cluster do HDInsight. Para obter mais informações, consulte [Use SSH Tunneling túnel com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Depois de criar um túnel, abra a IU web do Ambari no seu navegador da web com o URI `http://headnodehost:8080`.

3. Do lado esquerdo da página, selecione **Oozie** > **ligações rápidas** > **IU da Web de Oozie**.

    ![Imagem dos menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. A IU da web de Oozie por predefinição para apresentar as tarefas de fluxo de trabalho em execução. Para ver todas as tarefas de fluxo de trabalho, selecione **todas as tarefas**.

    ![Todas as tarefas apresentadas](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Para ver mais informações sobre uma tarefa, selecione a tarefa.

    ![Informações da tarefa](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Partir do **informações da tarefa** guia, pode ver as informações de trabalho básico e as ações individuais dentro da tarefa. Pode utilizar os separadores na parte superior para ver os **definição de tarefa**, **configuração de tarefa**, acesso a **registo da tarefa**, ou ver Acíclico (DAG) do trabalho em **Da tarefa DAG**.

   * **Registo da tarefa**: Selecione o **obter registos** botão para obter todos os registos para o trabalho ou utilizar o **introduza o filtro de pesquisa** campo para filtrar os registos.

       ![Registo da tarefa](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Tarefa DAG**: O DAG é uma visão geral gráfica dos caminhos de dados executadas pelo fluxo de trabalho.

       ![Tarefa DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Se selecionar uma das ações do **informações da tarefa** separador, ele apresenta informações para a ação. Por exemplo, selecione o **RunSqoopExport** ação.

    ![Informações de ação](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Pode ver os detalhes para a ação, como um link para o **URL da consola**. Utilize esta ligação para ver informações de controlador de tarefa para a tarefa.

## <a name="schedule-jobs"></a>Agendar tarefas

Pode utilizar o coordenador de especificar um início, fim e a frequência de ocorrência de tarefas. Para definir uma agenda para o fluxo de trabalho, conclua os seguintes passos:

1. Utilize o seguinte comando para criar um ficheiro denominado **coordinator.xml**:

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
    > O `${...}` variáveis são substituídas pelos valores na definição de tarefa no tempo de execução. As variáveis são:
    >
    > * `${coordFrequency}`: O tempo entre as instâncias da tarefa em execução.
    > * `${coordStart}`: A hora de início de tarefa.
    > * `${coordEnd}`: A hora de fim da tarefa.
    > * `${coordTimezone}`: Tarefas de coordenador estão num fuso de horário fixo com nenhuma horário de Verão, normalmente são representado utilizando a UTC. Este fuso horário é referido como o *fuso horário de processamento de Oozie.*
    > * `${wfPath}`: O caminho para o workflow.xml.

2. Para guardar o ficheiro, selecione Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.

3. Para copiar o ficheiro para o diretório de trabalho para esta tarefa, utilize o seguinte comando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Para modificar o `job.xml` ficheiro que criou anteriormente, utilize o seguinte comando:

    ```bash
    nano job.xml
    ```

    Efetue as seguintes alterações:

   * Para instruir o Oozie para executar o ficheiro de coordenador em vez do fluxo de trabalho, altere `<name>oozie.wf.application.path</name>` para `<name>oozie.coord.application.path</name>`.

   * Para definir o `workflowPath` variável usada pelo coordenador, adicione o seguinte XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Substitua o `wasbs://mycontainer@mystorageaccount.blob.core.windows` texto com o valor utilizado em outras entradas no arquivo job.xml.

   * Para definir o início, fim e a frequência para o coordenador, adicionam o seguinte XML:

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

       Esses valores definidos às 12:00 de 10 de Maio de 2018, a hora de início e a hora de fim de 12 de Maio de 2018. O intervalo para executar esta tarefa é definido como diariamente. A frequência é em minutos, por isso, 24 horas x 60 minutos = 1440 minutos. Por fim, o fuso horário é definido como UTC.

5. Para guardar o ficheiro, selecione Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.

6. Para submeter e iniciar a tarefa, utilize o seguinte comando:

    ```bash
    oozie job -config job.xml -run
    ```

7. Se for para o Oozie web da interface do Usuário e selecione o **tarefas de coordenador** separador, verá informações como na imagem seguinte:

    ![Separador de tarefas de coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    O **Materialization seguinte** entrada contém da próxima vez que a tarefa é executada.

8. Como a tarefa de fluxo de trabalho anterior, se selecionar a entrada da tarefa na IU da web apresenta informações sobre a tarefa:

    ![Informações da tarefa de coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]  
    > Esta imagem mostra apenas execuções com êxito da tarefa, não as ações individuais do fluxo de trabalho agendado. Para ver as ações individuais, selecione uma da **ação** entradas.

    ![Informações de ação](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Resolução de problemas

Com a interface do Usuário do Oozie, pode ver registos de Oozie. A interface do Usuário do Oozie também contém links para os registos de JobTracker para as tarefas de MapReduce que foram iniciados pelo fluxo de trabalho. O padrão para resolução de problemas deve ser:

   1. Ver a tarefa na IU da web do Oozie.

   2. Se existir um erro ou a falha de uma ação específica, selecione a ação para ver se o **mensagem de erro** campo fornece mais informações sobre a falha.

   3. Se estiver disponível, utilize o URL da ação para ver mais detalhes, como os registos de JobTracker, para a ação.

Seguem-se erros específicos que podem surgir e como resolvê-los.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Não é possível inicializar o cluster

**Os sintomas**: O estado da tarefa é alterado para **suspenso**. Detalhes para mostrar a tarefa a `RunHiveScript` estado como **START_MANUAL**. Selecionar a ação de apresenta a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Motivo**: Os endereços de armazenamento de Blobs do Azure utilizados no **job.xml** ficheiro não contém o contentor de armazenamento ou o nome de conta de armazenamento. O formato de endereço do armazenamento de BLOBs tem de ser `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Resolução**: Altere os endereços de armazenamento de BLOBs que utiliza a tarefa.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie não tem permissão para representar &lt;utilizador&gt;

**Os sintomas**: O estado da tarefa é alterado para **suspenso**. Detalhes para mostrar a tarefa a `RunHiveScript` estado como **START_MANUAL**. Se selecionar a ação, mostra a mensagem de erro seguinte:

    JA002: User: oozie is not allowed to impersonate <USER>

**Motivo**: As definições de permissão atuais não permitem Oozie representar a conta de utilizador especificado.

**Resolução**: Oozie pode representar os utilizadores a **utilizadores** grupo. Utilize o `groups USERNAME` para ver os grupos de que a conta de utilizador é membro. Se o utilizador não é um membro do **utilizadores** grupo, utilize o seguinte comando para adicionar o utilizador ao grupo:

    sudo adduser USERNAME users

> [!NOTE]  
> Pode demorar vários minutos antes de HDInsight reconhece que o utilizador foi adicionado ao grupo.

### <a name="launcher-error-sqoop"></a>Iniciador de erro (Sqoop)

**Os sintomas**: O estado da tarefa é alterado para **KILLED**. Detalhes para mostrar a tarefa a `RunSqoopExport` estado como **erro**. Se selecionar a ação, mostra a mensagem de erro seguinte:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Motivo**: Sqoop não consegue carregar o driver de base de dados necessário para acessar o banco de dados.

**Resolução**: Quando utilizar o Sqoop a partir de uma tarefa de Oozie, tem de incluir o controlador de base de dados com os outros recursos, como o workflow.xml, as utilizações de tarefa. Além disso, o arquivo que contém o driver de base de dados a partir de referência a `<sqoop>...</sqoop>` seção o workflow.xml.

Por exemplo, para a tarefa neste documento, usaria os seguintes passos:

1. Copiar o `mssql-jdbc-7.0.0.jre8.jar` do ficheiro para o **/tutoriais/useoozie** diretório:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Modificar a `workflow.xml` para adicionar o seguinte XML numa nova linha acima `</sqoop>`:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como definir um fluxo de trabalho do Oozie e como executar uma tarefa de Oozie. Para saber mais sobre como trabalhar com o HDInsight, veja os artigos seguintes:

* [Utilizar o coordenador de Oozie baseados no tempo do Apache com o HDInsight][hdinsight-oozie-coordinator-time]
* [Carregar dados para as tarefas do Apache Hadoop no HDInsight][hdinsight-upload-data]
* [Utilizar o Apache Sqoop com o Apache Hadoop no HDInsight][hdinsight-use-sqoop]
* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight][hdinsight-use-hive]
* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para o HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
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

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
