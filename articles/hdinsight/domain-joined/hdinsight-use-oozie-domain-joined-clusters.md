---
title: Fluxos de trabalho Apache Oozie com o Enterprise Security Package - Azure HDInsight seguros
description: Proteja os fluxos de trabalho Apache Oozie com o Azure HDInsight Enterprise Security Package. Saiba como definir um fluxo de trabalho do Oozie e submeter uma tarefa de Oozie.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 15cdc78559a8f299e2bf0f357bbb7c0664881712
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116899"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Execute Apache Oozie no Hadoop do HDInsight clusters com o Enterprise Security Package

Apache Oozie é um sistema de fluxo de trabalho e a coordenação que gere as tarefas do Apache Hadoop. Oozie está integrado com a pilha do Hadoop e suporta as seguintes tarefas:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Também pode utilizar o Oozie para agendar tarefas que são específicas para um sistema, como programas de Java ou scripts de shell.

## <a name="prerequisite"></a>Pré-requisito

- Um cluster de Hadoop do Azure HDInsight com o pacote de segurança da empresa (ESP). Ver [clusters do HDInsight configurar com ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Para obter instruções detalhadas sobre como utilizar o Oozie em clusters não ESP, consulte [utilização Apache Oozie fluxos de trabalho no HDInsight do Azure baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Ligar a um cluster do ESP

Para obter mais informações sobre Secure Shell (SSH), consulte [ligar ao HDInsight (Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ligar ao cluster do HDInsight com SSH:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. Para verificar a autenticação de Kerberos com êxito, utilize o `klist` comando. Se não, utilizar `kinit` para iniciar a autenticação Kerberos.

3. Iniciar sessão para o gateway do HDInsight para registar o token de OAuth necessário para acessar o armazenamento do Azure Data Lake:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Um código de estado de resposta **200 OK** indica o registo com êxito. Verifique o nome de utilizador e palavra-passe se for recebida uma resposta não autorizada, como 401.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho
Definições de fluxo de trabalho de Oozie são escritas na linguagem de definição de processo do Apache Hadoop (hPDL). hPDL é uma linguagem de definição do processo XML. Siga os passos seguintes para definir o fluxo de trabalho:

1. Configure a área de trabalho de um utilizador de domínio:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Substitua `DomainUser` com o nome de utilizador de domínio. 
   Substitua `DomainUserPath` com o caminho do diretório raiz para o utilizador de domínio. 
   Substitua `ClusterVersion` com a sua versão do cluster Hortonworks Data Platform (HDP).

2. Utilize a seguinte instrução para criar e editar um novo ficheiro:
   ```bash
   nano workflow.xml
   ```

3. Depois de abre o editor nano, introduza o seguinte XML como o conteúdo do ficheiro:
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
   ```
4. Substitua `clustername` com o nome do cluster. 

5. Para guardar o ficheiro, selecione Ctrl + X. Introduza `Y`. Em seguida, selecione **Enter**.

    O fluxo de trabalho é dividido em duas partes:
   * **Secção de credenciais.** Esta secção utiliza as credenciais que são utilizados para autenticar o Oozie ações:

     Este exemplo utiliza a autenticação para ações do Hive. Para obter mais informações, consulte [autenticação de ação](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     O serviço de credencial permite as ações de Oozie representar o utilizador para aceder aos serviços do Hadoop.

   * **Secção de ação.** Esta secção tem três ações: mapreduce, Hive, o servidor 2 e o servidor de ramo de registo 1:

     - O mapreduce execuções de ação um exemplo de um pacote de Oozie para mapreduce, que produz a contagem de palavras agregados.

     - O servidor do Hive 2 e ações de ramo de registo do servidor 1 executadas uma consulta numa tabela do Hive de exemplo fornecida com o HDInsight.

     As ações de Hive utilizam as credenciais definidas na secção de credenciais para autenticação ao utilizar a palavra-chave `cred` do elemento de ação.

6. Utilize o comando seguinte para copiar o `workflow.xml` de ficheiros para `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Substitua `domainuser` com seu nome de utilizador para o domínio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definir o ficheiro de propriedades para a tarefa de Oozie

1. Utilize a seguinte instrução para criar e editar um novo arquivo de propriedades da tarefa:

   ```bash
   nano job.properties
   ```

2. Depois de abre o editor nano, utilize o seguinte XML como o conteúdo do ficheiro:

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Utilize o `adl://home` URI para o `nameNode` propriedade se tiver Gen1 de armazenamento do Azure Data Lake como o armazenamento de cluster principal. Se estiver a utilizar o armazenamento de Blobs do Azure, em seguida, altere esta opção para `wasb://home`. Se estiver a utilizar Gen2 de armazenamento do Azure Data Lake, em seguida, alterar essa opção para `abfs://home`.
   * Substitua `domainuser` com seu nome de utilizador para o domínio.  
   * Substitua `ClusterShortName` com o nome abreviado para o cluster. Por exemplo, se o nome do cluster é https:// *[link de exemplo]* sechadoopcontoso.azurehdisnight.net, o `clustershortname` é os primeiro de seis carateres do cluster: **sechad**.  
   * Substitua `jdbcurlvalue` com o URL de JDBC da configuração do Hive. Um exemplo é jdbc:hive2: / / headnodehost:10001 /; transportMode = http.      
   * Para guardar o ficheiro, selecione Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.

   Este ficheiro de propriedades tem de existir localmente ao executar os trabalhos de Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Criar scripts de Hive personalizados para as tarefas de Oozie

Pode criar os dois scripts de Hive para servidor do Hive 1 e servidor do Hive 2, conforme mostrado nas seções a seguir.

### <a name="hive-server-1-file"></a>Ficheiro de 1 de servidor do Hive

1.  Criar e editar um ficheiro para ações de servidor 1 do Hive:
    ```bash
    nano countrowshive1.hql
    ```

2.  Crie o script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Guarde o ficheiro para o Apache Hadoop Distributed File System (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Ficheiro de servidor 2 do Hive

1.  Criar e editar um campo para ações de servidor 2 do Hive:
    ```bash
    nano countrowshive2.hql
    ```

2.  Crie o script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Guarde o ficheiro HDFS:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Submeter tarefas de Oozie

Submeter tarefas de Oozie para clusters do ESP é como o envio de trabalhos de Oozie no clusters não ESP.

Para obter mais informações, consulte [utilização Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight do Azure baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultados de uma submissão de tarefas do Oozie
Trabalhos de Oozie são executados para o utilizador. Então, Apache YARN do Hadoop e Apache Ranger auditoria registos show as tarefas em execução como o utilizador representado. A saída de interface de linha de comandos de uma tarefa de Oozie é semelhante ao seguinte código:



```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

Os registos de auditoria do Ranger para ações de servidor 2 Hive mostram Oozie executar a ação do utilizador. As exibições do Ranger e YARN são visíveis apenas para o administrador de cluster.

## <a name="configure-user-authorization-in-oozie"></a>Configurar a autorização do utilizador no Oozie

Oozie por si só tem uma configuração de autorização de utilizador que pode impedir que os utilizadores a parar ou eliminar tarefas de outros usuários. Para ativar esta configuração, defina o `oozie.service.AuthorizationService.security.enabled` para `true`. 

Para obter mais informações, consulte [Apache Oozie instalação e configuração](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Para componentes, como o servidor do Hive 1 em que o Ranger Plug-in não está disponível ou suportado, apenas de alta granularidade autorização HDFS, é possível. Autorização detalhada está disponível apenas através de plug-ins do Ranger.

## <a name="get-the-oozie-web-ui"></a>Obter a IU da web de Oozie

A IU da web de Oozie fornece uma vista baseada na web para o estado das tarefas de Oozie no cluster. Para obter a IU da web, siga os passos seguintes em ESP clusters:

1. Adicionar uma [nó de extremidade](../hdinsight-apps-use-edge-node.md) e ative [autenticação SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Siga os [IU da web do Oozie](../hdinsight-use-oozie-linux-mac.md) passos para ativar o túnel de SSH ao nó de extremidade e aceder à IU da web.

## <a name="next-steps"></a>Passos Seguintes
* [Utilizar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight do Azure baseado em Linux](../hdinsight-use-oozie-linux-mac.md).
* [Utilize o coordenador de Apache Oozie baseados no tempo](../hdinsight-use-oozie-coordinator-time.md).
* [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
