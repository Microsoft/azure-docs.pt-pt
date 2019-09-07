---
title: Fluxos de trabalho do Apache Oozie com o Enterprise Security Package-Azure HDInsight
description: Proteja os fluxos de trabalho do Apache Oozie usando o Azure HDInsight Enterprise Security Package. Saiba como definir um fluxo de trabalho do Oozie e enviar uma tarefa do Oozie.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: ea724a9bc8ddd92f04a781d5c3ce9bc08a35312e
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734867"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Executar o Apache Oozie em clusters Hadoop do HDInsight com Enterprise Security Package

O Apache Oozie é um sistema de fluxo de trabalho e coordenação que gerencia Apache Hadoop trabalhos. O Oozie é integrado à pilha do Hadoop e dá suporte aos seguintes trabalhos:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Você também pode usar o Oozie para agendar trabalhos que são específicos de um sistema, como programas Java ou scripts de Shell.

## <a name="prerequisite"></a>Pré-requisito

- Um cluster Azure HDInsight Hadoop com Enterprise Security Package (ESP). Consulte [Configurar clusters HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Para obter instruções detalhadas sobre como usar o Oozie em clusters não-ESP, consulte [usar fluxos de trabalho do Apache Oozie no Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Conectar-se a um cluster ESP

Para obter mais informações sobre Secure Shell (SSH), consulte [conectar-se ao HDInsight (Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Conecte-se ao cluster HDInsight usando SSH:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. Para verificar a autenticação Kerberos bem-sucedida, use `klist` o comando. Caso contrário, use `kinit` para iniciar a autenticação Kerberos.

3. Entre no gateway do HDInsight para registrar o token OAuth necessário para acessar o Azure Data Lake Storage:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Um código de resposta de status de **200 OK** indica um registro bem-sucedido. Verifique o nome de usuário e a senha se uma resposta não autorizada for recebida, como 401.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho
As definições de fluxo de trabalho Oozie são escritas em Apache Hadoop hPDL (linguagem de definição de processo). hPDL é uma linguagem de definição de processo XML. Execute as seguintes etapas para definir o fluxo de trabalho:

1. Configurar um espaço de trabalho do usuário do domínio:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Substituir `DomainUser` pelo nome de usuário do domínio. 
   Substitua `DomainUserPath` pelo caminho do diretório base do usuário do domínio. 
   Substitua `ClusterVersion` pela versão do cluster Hortonworks Data Platform (HDP).

2. Use a seguinte instrução para criar e editar um novo arquivo:
   ```bash
   nano workflow.xml
   ```

3. Depois que o editor do nano for aberto, insira o seguinte XML como o conteúdo do arquivo:
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
4. Substituir `clustername` pelo nome do cluster. 

5. Para salvar o arquivo, selecione CTRL + X. Introduza `Y`. Em seguida, selecione **Enter**.

    O fluxo de trabalho é dividido em duas partes:
   * **Seção de credencial.** Esta seção usa as credenciais que são usadas para autenticar ações Oozie:

     Este exemplo usa autenticação para ações do hive. Para saber mais, confira [autenticação de ação](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     O serviço de credenciais permite que as ações do Oozie representem o usuário para acessar os serviços do Hadoop.

   * **Seção de ação.** Esta seção tem três ações: map-reduza, Hive Server 2 e Hive Server 1:

     - A ação mapear-reduzir executa um exemplo de um pacote Oozie para a redução de mapa que gera a contagem de palavras agregadas.

     - As ações do servidor Hive 2 e do hive 1 executam uma consulta em uma tabela hive de exemplo fornecida com o HDInsight.

     As ações do hive usam as credenciais definidas na seção credenciais para autenticação usando a palavra- `cred` chave no elemento Action.

6. Use o seguinte comando para copiar o `workflow.xml` arquivo para `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Substitua `domainuser` pelo nome de usuário do domínio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definir o arquivo de propriedades para o trabalho Oozie

1. Use a instrução a seguir para criar e editar um novo arquivo para as propriedades do trabalho:

   ```bash
   nano job.properties
   ```

2. Depois que o editor do nano for aberto, use o seguinte XML como o conteúdo do arquivo:

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

   * Use o `adl://home` URI para a `nameNode` Propriedade se você tiver Azure data Lake Storage Gen1 como o armazenamento de cluster primário. Se você estiver usando o armazenamento de BLOBs do Azure, `wasb://home`altere-o para. Se você estiver usando Azure Data Lake Storage Gen2, altere para `abfs://home`.
   * Substitua `domainuser` pelo nome de usuário do domínio.  
   * Substituir `ClusterShortName` pelo nome curto do cluster. Por exemplo, se o nome do cluster for https:// *[exemplo link]* sechadoopcontoso.azurehdisnight.net, `clustershortname` os primeiros seis caracteres do cluster: **sechad**.  
   * Substitua `jdbcurlvalue` pela URL JDBC da configuração do hive. Um exemplo é JDBC: hive2://headnodehost: 10001/; transportmode = http.      
   * Para salvar o arquivo, selecione CTRL + X, digite `Y`e, em seguida, selecione **Enter**.

   Esse arquivo de propriedades precisa estar presente localmente ao executar trabalhos do Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Criar scripts do hive personalizados para trabalhos do Oozie

Você pode criar os dois scripts do hive para o hive Server 1 e o servidor Hive 2, conforme mostrado nas seções a seguir.

### <a name="hive-server-1-file"></a>Arquivo do hive Server 1

1.  Crie e edite um arquivo para as ações do hive Server 1:
    ```bash
    nano countrowshive1.hql
    ```

2.  Crie o script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Salve o arquivo em Apache Hadoop Sistema de Arquivos Distribuído (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Arquivo do hive Server 2

1.  Crie e edite um campo para as ações do hive Server 2:
    ```bash
    nano countrowshive2.hql
    ```

2.  Crie o script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Salve o arquivo no HDFS:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Enviar trabalhos do Oozie

O envio de trabalhos do Oozie para clusters ESP é como o envio de trabalhos do Oozie em clusters não ESP.

Para obter mais informações, consulte [usar o Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultados de um envio de trabalho Oozie
Os trabalhos do Oozie são executados para o usuário. Portanto, os logs de auditoria Apache Hadoop YARN e Apache Ranger mostram os trabalhos que estão sendo executados como o usuário representado. A saída da interface de linha de comando de um trabalho Oozie é semelhante ao seguinte código:



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

Os logs de auditoria do Ranger para ações do servidor Hive 2 mostram Oozie executando a ação para o usuário. As exibições do Ranger e do YARN são visíveis apenas para o administrador do cluster.

## <a name="configure-user-authorization-in-oozie"></a>Configurar a autorização do usuário no Oozie

O Oozie sozinho tem uma configuração de autorização de usuário que pode impedir que os usuários interrompam ou excluam os trabalhos de outros usuários. Para habilitar essa configuração, defina `oozie.service.AuthorizationService.security.enabled` como. `true` 

Para obter mais informações, consulte [instalação e configuração do Apache Oozie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Para componentes como o servidor do hive 1 em que o plug-in do Ranger não está disponível ou com suporte, apenas a autorização HDFS de alta granularidade é possível. A autorização refinada está disponível somente por meio de plug-ins do Ranger.

## <a name="get-the-oozie-web-ui"></a>Obter a interface do usuário da Web do amOozie

A interface do usuário da Web do amOozie fornece uma exibição baseada na Web para o status de trabalhos do Oozie no cluster. Para obter a interface do usuário da Web, execute as seguintes etapas em clusters ESP:

1. Adicione um [nó de borda](../hdinsight-apps-use-edge-node.md) e habilite a [autenticação Kerberos SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Siga as etapas da [interface do usuário da Web do amOozie](../hdinsight-use-oozie-linux-mac.md) para habilitar o túnel SSH para o nó de borda e acessar a interface do usuário da Web.

## <a name="next-steps"></a>Passos Seguintes
* [Use o Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).
* [Conecte-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
