---
title: Solucionar problemas do HBase usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o HBase e o Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 13a4831d946eb7e25e586cafae4cae51b49fd8a7
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780764"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Solucionar problemas do Apache HBase usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas resoluções ao trabalhar com cargas do Apache HBase no Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Como fazer executar relatórios de comando hbck com várias regiões não atribuídas?

Uma mensagem de erro comum que você pode ver ao executar o `hbase hbck` comando é "várias regiões que estão sendo não atribuídas ou buracos na cadeia de regiões".

Na interface do usuário do HBase Master, você pode ver o número de regiões desbalanceadas em todos os servidores de região. Em seguida, você pode `hbase hbck` executar o comando para ver os buracos na cadeia de região.

Os buracos podem ser causados por regiões offline, portanto, corrija as atribuições primeiro. 

Para colocar as regiões não atribuídas novamente em um estado normal, conclua as seguintes etapas:

1. Entre no cluster HBase do HDInsight usando SSH.
2. Para se conectar com o Shell do Apache ZooKeeper, `hbase zkcli` execute o comando.
3. Execute o `rmr /hbase/regions-in-transition` comando ou o `rmr /hbase-unsecure/regions-in-transition` comando.
4. Para sair do `hbase zkcli` Shell, use o `exit` comando.
5. Abra a interface do usuário do Apache Ambari e reinicie o serviço do Active HBase Master.
6. Execute o `hbase hbck` comando novamente (sem nenhuma opção). Verifique a saída desse comando para garantir que todas as regiões estejam sendo atribuídas.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Como fazer corrigir problemas de tempo limite ao usar comandos hbck para atribuições de região?

### <a name="issue"></a>Problema

Uma possível causa de problemas de tempo limite quando você `hbck` usa o comando pode ser que várias regiões estejam no estado "em transição" por um longo tempo. Você pode ver essas regiões offline na interface do usuário do HBase Master. Como um grande número de regiões está tentando fazer a transição, HBase Master pode atingir o tempo limite e não pode colocar essas regiões novamente online.

### <a name="resolution-steps"></a>Passos de resolução

1. Entre no cluster HBase do HDInsight usando SSH.
2. Para se conectar com o Shell do Apache ZooKeeper, `hbase zkcli` execute o comando.
3. Execute o `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` comando ou.
4. Para sair do `hbase zkcli` Shell, use o `exit` comando.
5. Na interface do usuário do amAmbari, reinicie o serviço do Active HBase Master.
6. Execute o `hbase hbck -fixAssignments` comando novamente.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Como fazer forçar a desabilitar o modo de segurança do HDFS em um cluster?

### <a name="issue"></a>Problema

O HDFS (Apache Hadoop local Sistema de Arquivos Distribuído) está preso no modo de segurança no cluster HDInsight.

### <a name="detailed-description"></a>Descrição detalhada

Esse erro pode ser causado por uma falha ao executar o seguinte comando HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

O erro que você pode ver ao tentar executar o comando é semelhante ao seguinte:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Causa provável

O cluster HDInsight foi reduzido para um número muito pequeno de nós. O número de nós está abaixo ou perto do fator de replicação do HDFS.

### <a name="resolution-steps"></a>Passos de resolução 

1. Obtenha o status do HDFS no cluster HDInsight executando os seguintes comandos:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Você também pode verificar a integridade do HDFS no cluster HDInsight usando os seguintes comandos:

   ```apache
   hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Se você determinar que não há nenhum bloco ausente, corrompido ou em replicado, ou que esses blocos possam ser ignorados, execute o seguinte comando para retirar o nó de nome do modo de segurança:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Como fazer corrigir problemas de conectividade JDBC ou sqlline com o Apache Phoenix?

### <a name="resolution-steps"></a>Passos de resolução

Para se conectar com Apache Phoenix, você deve fornecer o endereço IP de um nó de Apache ZooKeeper ativo. Verifique se o serviço ZooKeeper ao qual o sqlline.py está tentando se conectar está em execução.
1. Entre no cluster HDInsight usando SSH.
2. Introduza o seguinte comando:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Você pode obter o endereço IP do nó active ZooKeeper da interface do usuário do amAmbari. Acesse o **HBase** > **Quick links** > **ZK\* (Active)**  > **Zookeeper info**. 

3. Se o sqlline.py se conectar ao Phoenix e não tiver tempo limite, execute o seguinte comando para validar a disponibilidade e a integridade do Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Se esse comando funcionar, não haverá nenhum problema. O endereço IP fornecido pelo usuário pode estar incorreto. No entanto, se o comando pausar por um tempo estendido e, em seguida, exibir o erro a seguir, vá para a etapa 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Execute os seguintes comandos do nó principal (hn0) para diagnosticar a condição do sistema Phoenix. Tabela de catálogo:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   O comando deve retornar um erro semelhante ao seguinte: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Na interface do usuário do Apache Ambari, conclua as seguintes etapas para reiniciar o serviço HMaster em todos os nós do ZooKeeper:

    1. Na seção de **Resumo** do HBase, vá para **HBase** > **Active HBase Master**. 
    2. Na seção **componentes** , reinicie o serviço HBase Master.
    3. Repita essas etapas para todos os serviços de **HBase Master em espera** restantes. 

Pode levar até cinco minutos para o serviço de HBase Master estabilizar e concluir o processo de recuperação. Após alguns minutos, repita os comandos sqlline.py para confirmar que o sistema. A tabela de catálogo está ativa e pode ser consultada. 

Quando o sistema. A tabela de catálogo volta a normal, o problema de conectividade para Phoenix deve ser resolvido automaticamente.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>O que faz com que um servidor mestre falhe ao iniciar?

### <a name="error"></a>Erro 

Ocorre uma falha de renomeação atômica.

### <a name="detailed-description"></a>Descrição detalhada

Durante o processo de inicialização, o HMaster conclui muitas etapas de inicialização. Isso inclui a movimentação de dados da pasta de rascunho (. tmp) para a pasta de dados. HMaster também examina a pasta logs write-ahead (WALs) para ver se há servidores de região sem resposta e assim por diante. 

Durante a inicialização, o HMaster faz `list` um comando básico nessas pastas. Se, a qualquer momento, o HMaster vir um arquivo inesperado em qualquer uma dessas pastas, ele lançará uma exceção e não será iniciado.  

### <a name="probable-cause"></a>Causa provável

Nos logs do servidor de região, tente identificar a linha do tempo da criação do arquivo e, em seguida, veja se houve uma falha no processo na hora em que o arquivo foi criado. (Entre em contato com o suporte do HBase para ajudá-lo a fazer isso.) Isso nos ajuda a fornecer mecanismos mais robustos, para que você possa evitar atingir esse bug e garantir desligamentos de processos normais.

### <a name="resolution-steps"></a>Passos de resolução

Verifique a pilha de chamadas e tente determinar qual pasta pode estar causando o problema (por exemplo, pode ser a pasta WALs ou a pasta. tmp). Em seguida, no Cloud Explorer ou usando comandos do HDFS, tente localizar o arquivo de problema. Normalmente, esse é um \*arquivo-renamePending. JSON. (O \*arquivo-renamePending. JSON é um arquivo de diário que é usado para implementar a operação de renomeação atômica no driver WASB. Devido a bugs nessa implementação, esses arquivos podem ser deixados após a falha do processo e assim por diante.) Force-exclui esse arquivo no Cloud Explorer ou usando comandos do HDFS. 

Às vezes, também pode haver um arquivo temporário chamado algo como *$ $ $. $ $ $* neste local. Você precisa usar o comando `ls` HDFS para ver esse arquivo; você não pode ver o arquivo no Cloud Explorer. Para excluir esse arquivo, use o comando `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`HDFS.  

Depois de executar esses comandos, o HMaster deve iniciar imediatamente. 

### <a name="error"></a>Erro

Nenhum endereço de servidor está listado em *HBase: meta* para a região xxx.

### <a name="detailed-description"></a>Descrição detalhada

Você pode ver uma mensagem em seu cluster do Linux que indica que o *HBase: meta* table não está online. A `hbck` execução pode relatar que o "HBase: MetaTable replicaId 0 não foi encontrado em nenhuma região". O problema pode ser que HMaster não pôde inicializar após a reinicialização do HBase. Nos logs do HMaster, você pode ver a mensagem: "Nenhum endereço de servidor listado no HBase: meta para região HBase: \<nome\>da região de backup".  

### <a name="resolution-steps"></a>Passos de resolução

1. No Shell do HBase, insira os seguintes comandos (altere os valores reais conforme aplicável):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Exclua a entrada *HBase: namespace* . Essa entrada pode ser o mesmo erro que está sendo relatado quando a tabela *HBase: namespace* é verificada.

3. Para ativar o HBase em um estado de execução, na interface do usuário do amAmbari, reinicie o serviço active HMaster.  

4. No Shell do HBase, para abrir todas as tabelas offline, execute o seguinte comando:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Leitura adicional

[Não é possível processar a tabela do HBase](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Erro

O HMaster expira com uma exceção fatal semelhante a "Java. IO. IOException: TimedOut 300000ms aguardando a tabela de namespace ser atribuída. "

### <a name="detailed-description"></a>Descrição detalhada

Você poderá enfrentar esse problema se tiver muitas tabelas e regiões que não foram liberadas quando você reiniciar os serviços HMasters. A reinicialização pode falhar e você verá a mensagem de erro anterior.  

### <a name="probable-cause"></a>Causa provável

Esse é um problema conhecido com o serviço HMaster. As tarefas gerais de inicialização do cluster podem levar muito tempo. O HMaster é desligado porque a tabela de namespace ainda não está atribuída. Isso ocorre apenas em cenários em que há grande quantidade de dados não liberados, e um tempo limite de cinco minutos não é suficiente.
  
### <a name="resolution-steps"></a>Passos de resolução

1. Na interface do usuário do Apache Ambari, vá para**configurações**do **HBase** > . No arquivo HBase-site. xml personalizado, adicione a seguinte configuração: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Reinicie os serviços necessários (HMaster e possivelmente outros serviços do HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>O que causa uma falha de reinicialização em um servidor de região?

### <a name="issue"></a>Problema

Uma falha de reinicialização em um servidor de região pode ser evitada seguindo as práticas recomendadas. Recomendamos que você Pause a atividade de carga de trabalho pesada quando estiver planejando reiniciar os servidores de região do HBase. Se um aplicativo continuar se conectando a servidores de região quando o desligamento estiver em andamento, a operação de reinicialização do servidor de região será mais lenta em vários minutos. Além disso, é uma boa ideia primeiro liberar todas as tabelas. Para obter uma referência sobre como liberar tabelas, consulte [HDInsight HBase: Como melhorar o tempo de reinicialização do cluster do Apache HBase](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)liberando tabelas.

Se você iniciar a operação de reinicialização em servidores de região do HBase da interface do usuário do Apache Ambari, verá imediatamente que os servidores de região foram desativados, mas eles não são reiniciados imediatamente. 

Veja o que acontece nos bastidores: 

1. O agente Ambari envia uma solicitação de interrupção para o servidor de região.
2. O agente Ambari aguarda 30 segundos para que o servidor de região seja desligado normalmente. 
3. Se o seu aplicativo continuar a se conectar com o servidor de região, o servidor não será desligado imediatamente. O tempo limite de 30 segundos expira antes de o desligamento ocorrer. 
4. Após 30 segundos, o agente Ambari envia um comando Force-Kill`kill -9`() para o servidor de região. Você pode ver isso no log do ambari-Agent (no diretório/var/log/do respectivo nó de trabalho):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   Devido ao desligamento abrupta, a porta associada ao processo pode não ser liberada, embora o processo do servidor de região seja interrompido. Essa situação pode levar a um AddressBindException quando o servidor de região estiver sendo iniciado, conforme mostrado nos logs a seguir. Você pode verificar isso no Region-Server. log no diretório/var/log/HBase nos nós de trabalho em que o servidor de região falha ao iniciar. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Passos de resolução

1. Tente reduzir a carga nos servidores de região do HBase antes de iniciar uma reinicialização. 
2. Como alternativa (se a etapa 1 não ajudar), tente reiniciar manualmente os servidores de região nos nós de trabalho usando os seguintes comandos:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Consultar Também
[Resolver problemas com o Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
