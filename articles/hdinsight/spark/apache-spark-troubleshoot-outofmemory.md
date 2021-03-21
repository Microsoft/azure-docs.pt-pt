---
title: Exceções outOfMemoryError para Apache Spark em Azure HDInsight
description: Várias exceções outOfMemoryError para o cluster Apache Spark em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: a15d79f2ae9c3d20a73ec557c57a5c189b18111b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946351"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Exceções outOfMemoryError para Apache Spark em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Cenário: Exceção outOfMemoryError para a Faísca Apache

### <a name="issue"></a>Problema

A sua aplicação Apache Spark falhou com uma exceção outOfMemoryError sem manipulação. Pode receber uma mensagem de erro semelhante a:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Causa

A causa mais provável desta exceção é que não há memória suficiente para as máquinas virtuais java (JVMs). Estes JVMs são lançados como executores ou motoristas como parte da aplicação Apache Spark.

### <a name="resolution"></a>Resolução

1. Determine o tamanho máximo dos dados que a aplicação Spark vai processar. Faça uma estimativa do tamanho com base no tamanho máximo dos dados de entrada, os dados intermédios produzidos através da transformação dos dados de entrada e dos dados de saída produzidos transformando ainda mais os dados intermédios. Se a estimativa inicial não for suficiente, aumente ligeiramente o tamanho e itere até que os erros de memória diminuam.

1. Confirme que o cluster do HDInsight a ser utilizado tem recursos suficientes em termos de memória, assim como núcleos, para alojar a aplicação Spark. Isto pode ser determinado visualizando a secção de Métricas de Cluster do UI YARN do cluster para os valores de **Memória Utilizada** vs. **Memory Total** e **VCores Usado** vs. **VCores Total**.

    ![visão de memória núcleo de fios](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Desaperte as seguintes configurações de Faísca para valores apropriados. Equilibrar os requisitos de candidatura com os recursos disponíveis no cluster. Estes valores não devem exceder 90% da memória e dos núcleos disponíveis, tal como visto pela YARN, devendo igualmente satisfazer o requisito mínimo de memória da aplicação Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Memória total utilizada por todos os executores =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Memória total utilizada pelo condutor =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Cenário: Erro de espaço de java ao tentar abrir o servidor de histórico apache Spark

### <a name="issue"></a>Problema

Recebe o seguinte erro ao abrir eventos no servidor Spark History:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Causa

Esta questão é muitas vezes causada pela falta de recursos ao abrir grandes ficheiros de eventos de faíscas. O tamanho da pilha spark está definido para 1 GB por padrão, mas grandes ficheiros de evento spark podem exigir mais do que isso.

Se quiser verificar o tamanho dos ficheiros que está a tentar carregar, pode executar os seguintes comandos:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Resolução

Pode aumentar a memória do Servidor histórico de faíscas editando a `SPARK_DAEMON_MEMORY` propriedade na configuração Spark e reiniciando todos os serviços.

Pode fazê-lo a partir do UI do navegador Ambari selecionando a secção Spark2/Config/Advanced spark2-env.

![Secção avançada de spark2-env](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Adicione a seguinte propriedade para alterar a memória do Servidor histórico de faíscas de 1g para 4g: `SPARK_DAEMON_MEMORY=4g` .

![Propriedade Spark](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Certifique-se de reiniciar todos os serviços afetados a partir de Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Cenário: Livy Server falha no cluster Apache Spark

### <a name="issue"></a>Problema

Livy Server não pode ser iniciado num Apache Spark [(Faísca 2.1 em Linux (HDI 3.6)]. Tentar reiniciar resulta na seguinte pilha de erros, a partir dos registos Livy:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Causa

`java.lang.OutOfMemoryError: unable to create new native thread` destaques OS não pode atribuir mais fios nativos a JVMs. Confirmado que esta Exceção é causada pela violação do limite de contagem de fios por processo.

Quando livy Server termina inesperadamente, todas as ligações com Spark Clusters também são terminadas, o que significa que todos os empregos e dados relacionados serão perdidos. No mecanismo de recuperação da sessão hDP 2.6 foi introduzido, Livy armazena os detalhes da sessão no Zookeeper para ser recuperado após o Livy Server estar de volta.

Quando um grande número de empregos são submetidos via Livy, como parte da Alta Disponibilidade para Livy Server armazena estes estados de sessão em ZK (em clusters HDInsight) e recuperar essas sessões quando o serviço Livy é reiniciado. No recomeço após a interrupção inesperada, Livy cria um fio por sessão e isso acumula um certo número de sessões a serem recuperadas causando a criação de demasiados fios.

### <a name="resolution"></a>Resolução

Elimine todas as entradas utilizando passos detalhados abaixo.

1. Obtenha o endereço IP dos nóns do zookeeper usando

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Acima do comando listado todos os zookeepers para o meu aglomerado

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Obtenha todo o endereço IP dos nós do zookeeper usando ping Ou também pode ligar-se ao zookeeper a partir de headnode usando o nome zk

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Uma vez ligado ao zookeeper execute o seguinte comando para listar todas as sessões que são tentadas reiniciar.

    1. A maioria dos casos pode ser uma lista de mais de 8000 sessões ####

        ```bash
        ls /livy/v1/batch
        ```

    1. O comando seguinte é remover todas as sessões a recuperar. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Aguarde que o comando acima está concluído e o cursor para devolver o pedido e, em seguida, reiniciar o serviço Livy de Ambari, que deve ter sucesso.

> [!NOTE]
> `DELETE` a sessão livy uma vez que é concluída a sua execução. As sessões de lote Livy não serão apagadas automaticamente assim que a aplicação de faíscas estiver concluída, que é por design. Uma sessão Livy é uma entidade criada por um pedido POST contra o servidor Livy Rest. `DELETE`É necessária uma chamada para eliminar essa entidade. Ou devemos esperar que a GC entre em vigor.

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* [Visão geral da gestão da memória de faíscas](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Depuração da aplicação Spark em clusters HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).