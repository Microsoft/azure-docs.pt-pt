---
title: Fluxos de trabalho Apache Oozie & Segurança Empresarial - Azure HDInsight
description: Secure Apache Oozie workflows using the Azure HDInsight Enterprise Security Package. Aprenda a definir um fluxo de trabalho Oozie e submeta um trabalho Oozie.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: 559779e3979ab2ada2191f55cfc06ceb2470bd7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933478"
---
# <a name="run-apache-oozie-in-azure-hdinsight-clusters-with-enterprise-security-package"></a>Executar Apache Oozie em clusters Azure HDInsight com pacote de segurança empresarial

Apache Oozie é um sistema de trabalho e coordenação que gere os empregos da Apache Hadoop. Oozie está integrado com a pilha Hadoop, e apoia os seguintes trabalhos:

- Apache MapReduce
- Porco-apache
- Colmeia Apache
- Apache Sqoop

Você também pode usar Oozie para agendar trabalhos específicos de um sistema, como programas java ou scripts de concha.

## <a name="prerequisite"></a>Pré-requisito

Um cluster Azure HDInsight Hadoop com Pacote de Segurança Empresarial (ESP). Consulte [os clusters Configure HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Para obter instruções detalhadas sobre como utilizar o Oozie em clusters não-ESP, consulte [use fluxos de trabalho Apache Oozie em Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Ligar-se a um cluster ESP

Para obter mais informações sobre Secure Shell (SSH), consulte [Connect to HDInsight (Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ligue-se ao cluster HDInsight utilizando sSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Para verificar a autenticação bem sucedida de Kerberos, utilize o `klist` comando. Caso contrário, utilize `kinit` para iniciar a autenticação Kerberos.

1. Inscreva-se na porta de entrada HDInsight para registar o token OAuth necessário para aceder ao Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Um código de resposta ao estado de **200 OK** indica um registo bem sucedido. Verifique o nome de utilizador e a palavra-passe se for recebida uma resposta não autorizada, como o 401.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

As definições de fluxo de trabalho Oozie são escritas na Linguagem de Definição de Processo Apache Hadoop (hPDL). hPDL é uma linguagem de definição de processo XML. Tome as seguintes medidas para definir o fluxo de trabalho:

1. Configurar o espaço de trabalho de um utilizador de domínio:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   `DomainUser`Substitua-o pelo nome de utilizador do domínio.
   `DomainUserPath`Substitua-o pelo caminho do diretório doméstico para o utilizador de domínio.
   `ClusterVersion`Substitua-o pela versão da plataforma de dados do cluster.

2. Utilize a seguinte declaração para criar e editar um novo ficheiro:

   ```bash
   nano workflow.xml
   ```

3. Após a abertura do nano editor, insira o seguinte XML como o conteúdo do ficheiro:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
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

4. `clustername`Substitua-o pelo nome do cluster.

5. Para guardar o ficheiro, selecione **Ctrl+X**. Insira **Y**. Em seguida, **selecione Enter**.

    O fluxo de trabalho é dividido em duas partes:

   - **Credencial.** Esta secção requer as credenciais que são usadas para autenticar as ações da Oozie:

     Este exemplo utiliza a autenticação para ações de Colmeia. Para saber mais, consulte [a Autenticação de Ação.](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)

     O serviço de credenciais permite que as ações da Oozie se personiem ao utilizador para aceder aos serviços hadoop.

   - **A ação.** Esta secção tem três ações: redução de mapas, servidor hive 2 e servidor hive 1:

     - A ação de redução do mapa é um exemplo de um pacote Oozie para reduzir o mapa que produz a contagem de palavras agregada.

     - As ações do servidor Hive 2 e hive 1 executam uma consulta numa tabela de hive de amostra fornecida com HDInsight.

     As ações da Hive utilizam as credenciais definidas na secção de credenciais para autenticação utilizando a palavra-chave `cred` no elemento de ação.

6. Utilize o seguinte comando para copiar o `workflow.xml` ficheiro `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` para:

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. `domainuser`Substitua-o pelo seu nome de utilizador para o domínio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Defina o ficheiro de propriedades para o trabalho de Oozie

1. Utilize a seguinte declaração para criar e editar um novo ficheiro para propriedades de trabalho:

    ```bash
    nano job.properties
    ```

2. Após a abertura do nano editor, utilize o seguinte XML como o conteúdo do ficheiro:

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
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Use o `adl://home` URI para a `nameNode` propriedade se tiver a Azure Data Lake Storage Gen1 como armazenamento de cluster primário. Se estiver a utilizar o Azure Blob Storage, mude para `wasb://home` . Se estiver a usar a Azure Data Lake Storage Gen2, então mude para `abfs://home` .
   - `domainuser`Substitua-o pelo seu nome de utilizador para o domínio.
   - `ClusterShortName`Substitua-a pelo nome curto do cluster. Por exemplo, se o nome do cluster for https:// *[ligação de exemplo]* sechadoopcontoso.azurehdisnight.net, `clustershortname` são os primeiros seis caracteres do cluster: **sechad**.
   - `jdbcurlvalue`Substitua-a pelo URL JDBC da configuração da Colmeia. Um exemplo é jdbc:hive2://headnodehost:10001/;transportMode=http.
   - Para guardar o ficheiro, selecione Ctrl+X, introduza `Y` e, em seguida, **selecione Enter**.

   Este ficheiro de propriedades tem de estar presente localmente quando executa os empregos da Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Crie scripts personalizados da Hive para trabalhos Oozie

Pode criar os dois scripts Hive para o servidor Hive 1 e o servidor Hive 2, como mostrado nas seguintes secções.

### <a name="hive-server-1-file"></a>Ficheiro do servidor hive 1

1. Criar e editar um ficheiro para as ações do servidor Hive 1:

    ```bash
    nano countrowshive1.hql
    ```

2. Crie o script:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Guarde o ficheiro para o Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Ficheiro do servidor hive 2

1. Criar e editar um campo para as ações do servidor Hive 2:

    ```bash
    nano countrowshive2.hql
    ```

2. Crie o script:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Guarde o ficheiro para o HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Submeta empregos oozie

Apresentar empregos da Oozie para clusters esp é como apresentar empregos da Oozie em clusters não-ESP.

Para obter mais informações, consulte [Use Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultados de uma submissão de emprego da Oozie

Os empregos da Oozie são executados para o utilizador. Assim, tanto os registos de auditoria da Apache Hadoop YARN como do Apache Ranger mostram os empregos que estão a ser executados como utilizadores imitados. A saída da interface de linha de comando de um trabalho Oozie parece o seguinte código:

```output
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
ID                        Status    Ext ID            ExtStatus                 ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK    -                         OK             -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK    job_1529975666160_0051    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK    job_1529975666160_0053    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive    OK     job_1529975666160_0055    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end       OK     -                         OK             -
-----------------------------------------------------------------------------------------------
```

Os registos de auditoria do Ranger para as ações do servidor Hive 2 mostram o Oozie a executar a ação para o utilizador. As vistas ranger e YARN são visíveis apenas para a administração do cluster.

## <a name="configure-user-authorization-in-oozie"></a>Configure a autorização do utilizador em Oozie

A Oozie por si só tem uma configuração de autorização de utilizador que pode impedir os utilizadores de parar ou eliminar os empregos de outros utilizadores. Para ativar esta configuração, desa um pouco `oozie.service.AuthorizationService.security.enabled` de `true` . 

Para mais informações, consulte [a Instalação e Configuração Apache Oozie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Para componentes como o servidor Hive 1, onde o plug-in Ranger não está disponível ou suportado, apenas é possível uma autorização HDFS de grão grosso. A autorização de grãos finos só está disponível através de plug-ins Ranger.

## <a name="get-the-oozie-web-ui"></a>Obtenha a UI web Oozie

A UI web Oozie fornece uma visão web sobre o estado dos empregos de Oozie no cluster. Para obter a UI web, tome os seguintes passos em clusters ESP:

1. Adicione um [nó de borda](../hdinsight-apps-use-edge-node.md) e ative a [autenticação SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Siga os passos [de UI web Oozie](../hdinsight-use-oozie-linux-mac.md) para permitir o túnel de SSH até ao nó de borda e aceda à UI web.

## <a name="next-steps"></a>Próximos passos

- [Use Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).
- [Ligue-se ao HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
