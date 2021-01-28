---
title: Portas utilizadas pelos serviços hadoop em HDInsight - Azure
description: Este artigo fornece uma lista de portas usadas pelos serviços apache Hadoop em execução em Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b5dadb886078f701bb01447efd606c862ee73073
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945537"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portas utilizadas pelos serviços apache Hadoop em HDInsight

Este documento fornece uma lista das portas utilizadas pelos serviços Apache Hadoop em funcionamento em clusters HDInsight. Também fornece informações sobre portas utilizadas para se ligar ao cluster utilizando SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portos públicos vs. portos não públicos

Os clusters HDInsight baseados em Linux só expõem três portas publicamente na internet: 22, 23 e 443. Estas portas asseguram o acesso ao cluster utilizando SSH e serviços expostos através do protocolo HTTPS seguro.

HDInsight é implementado por várias Máquinas Virtuais Azure (nós de cluster) em execução numa Rede Virtual Azure. A partir da rede virtual, pode aceder a portas não expostas através da internet. Se ligar via SSH ao nó de cabeça, pode aceder diretamente aos serviços que correm nos nós do cluster.

> [!IMPORTANT]  
> Se não especificar uma Rede Virtual Azure como uma opção de configuração para HDInsight, uma é criada automaticamente. No entanto, não é possível juntar-se a outras máquinas (como outras Máquinas Virtuais Azure ou a máquina de desenvolvimento do seu cliente) a esta rede virtual.

Para juntar máquinas adicionais à rede virtual, tem de criar primeiro a rede virtual e, em seguida, especificá-la ao criar o seu cluster HDInsight. Para obter mais informações, consulte [Plan uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Portos públicos

Todos os nós de um cluster HDInsight estão localizados numa Rede Virtual Azure. Os nós não podem ser acedidos diretamente a partir da internet. Um gateway público fornece acesso à internet às seguintes portas, que são comuns em todos os tipos de cluster HDInsight.

| Serviço | Porta | Protocolo | Descrição |
| --- | --- | --- | --- |
| sshd |22 |SSH |Liga os clientes ao sshd no cabeçano primário. Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Liga os clientes ao sshd no nó de borda. Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Liga clientes ao sshd no headnode secundário. Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |UI web Ambari. Consulte [Manage HDInsight usando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Ver [Gerir HDInsight utilizando a API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Ver  [utilizar mapaReduce com curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Liga-se à Colmeia utilizando o ODBC. Consulte [o Connect Excel para HDInsight com o controlador Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Conecta-se ao ApacheHive usando jDBC. Consulte [a Ligação à Colmeia Apache em HDInsight utilizando o controlador Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Estão disponíveis para tipos específicos de agrupamentos:

| Serviço | Porta | Protocolo | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |HBase |HBase REST API. Ver [Começar a usar Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Faísca REPOUSO API. Ver [Submeter apache spark jobs remotamente usando Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Servidor Spark Thrift |443 |HTTPS |Spark |Servidor Spark Thrift usado para submeter consultas de Hive. Ver [Use Beeline com Colmeia Apache em HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Tempestade web UI. Ver [Implementar e gerir as topologias da Tempestade Apache em HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Procuração de repouso kafka |443 |HTTPS |Kafka |Kafka REST API. Consulte [interagir com clusters Apache Kafka em Azure HDInsight usando um representante de REST](kafka/rest-proxy.md) |

### <a name="authentication"></a>Autenticação

Todos os serviços expostos publicamente na internet devem ser autenticados:

| Porta | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de utilizador SSH especificadas durante a criação do cluster |
| 443 |O nome de login (padrão: administrador) e senha que foram definidas durante a criação do cluster |

## <a name="non-public-ports"></a>Portos não públicos

> [!NOTE]  
> Alguns serviços só estão disponíveis em tipos específicos de cluster. Por exemplo, a HBase só está disponível em tipos de cluster HBase.

> [!IMPORTANT]  
> Alguns serviços só funcionam com um headnode de cada vez. Se tentar ligar-se ao serviço no cabeçado primário e receber um erro, tente novamente utilizar o headnode secundário.

### <a name="ambari"></a>Ambari

| Serviço | Nós | Porta | Caminho url | Protocolo |
| --- | --- | --- | --- | --- |
| Ambari web UI | Nó de cabeça | 8080 | / | HTTP |
| Ambari REST API | Nó de cabeça | 8080 | /api/v1 | HTTP |

Exemplos:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Portas HDFS

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| II web NameNode |Nó de cabeça |30070 |HTTPS |UI web para ver o estado |
| Serviço de metadados NameNode |acenos de cabeça |8020 |IPC |Metadados do sistema de ficheiros |
| DataNode |Todos os nós operários |30075 |HTTPS |Web UI para ver estado, registos, e assim por diante. |
| DataNode |Todos os nós operários |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós operários |30020 |IPC |Operações de metadados |
| Nome secundárioNode |Nó de cabeça |50090 |HTTP |Checkpoint para Metadados de Nome |

### <a name="yarn-ports"></a>Portas YARN

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| UI web gestor de recursos |Nó de cabeça |8088 |HTTP |UI web para gestor de recursos |
| UI web gestor de recursos |Nó de cabeça |8090 |HTTPS |UI web para gestor de recursos |
| Interface de administração do Gestor de Recursos |acenos de cabeça |8141 |IPC |Para submissões de aplicações (Hive, hive server, Pig, e assim por diante.) |
| Programador de gestores de recursos |acenos de cabeça |8030 |HTTP |Interface administrativa |
| Interface de aplicação do Gestor de Recursos |acenos de cabeça |8050 |HTTP |Endereço da interface do gestor de aplicações |
| NodeManager |Todos os nós operários |30050 |&nbsp; |O endereço do gestor do contentor |
| NodeManager web UI |Todos os nós operários |30060 |HTTP |Interface do Gestor de Recursos |
| Endereço da linha do tempo |Nó de cabeça |10200 |RPC |O serviço RPC do serviço timeline. |
| UI web da linha do tempo |Nó de cabeça |8188 |HTTP |O serviço de linha do tempo web UI |

### <a name="hive-ports"></a>Portos de colmeia

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nó de cabeça |10001 |Thrift |Serviço de ligação à Colmeia (Thrift/JDBC) |
| Metastore do Hive |Nó de cabeça |9083 |Thrift |Serviço de ligação aos metadados da Colmeia (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Portas WebHCat

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor WebHCat |Nó de cabeça |30111 |HTTP |Web API em cima de HCatalog e outros serviços Hadoop |

### <a name="mapreduce-ports"></a>Portas MapReduce

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| História do Trabalho |Nó de cabeça |19888 |HTTP |MapReduce JobHstory web UI |
| História do Trabalho |Nó de cabeça |10020 |&nbsp; |MapReduce JobHstory servidor |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfere saídas de mapas intermédios para solicitar redutores |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor Oozie |Nó de cabeça |11000 |HTTP |URL para o serviço Oozie |
| Servidor Oozie |Nó de cabeça |11001 |HTTP |Porto para administrador de Oozie |

### <a name="ambari-metrics"></a>Métricas do Ambari

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| TimeLine (histórico de aplicações) |Nó de cabeça |6188 |HTTP |O serviço TimeLine web UI |
| TimeLine (histórico de aplicações) |Nó de cabeça |30200 |RPC |O serviço TimeLine web UI |

### <a name="hbase-ports"></a>Portas HBase

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nó de cabeça |16000 |&nbsp; |&nbsp; |
| HMaster info Web UI |Nó de cabeça |16010 |HTTP |A porta para a HBase Master web UI |
| Servidor da região |Todos os nós operários |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes usam para ligar ao ZooKeeper |

### <a name="kafka-ports"></a>Portos de Kafka

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Corretor |Nódoas operárias |9092 |Protocolo de Fio Kafka |Usado para comunicação com clientes |
| &nbsp; |Nódoa de zookeeper |2181 |&nbsp; |O porto que os clientes usam para ligar ao Zookeeper |
| Procuração DE REPOUSO | Nódes de gestão kafka |9400 |HTTPS |[Especificação kafka REST](/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Portas de faísca

| Serviço | Nós | Porta | Protocolo | Caminho url | Descrição |
| --- | --- | --- | --- | --- | --- |
| Servidores Spark Thrift |Nó de cabeça |10002 |Thrift | &nbsp; | Serviço de ligação ao Spark SQL (Thrift/JDBC) |
| Servidor Livy | Nó de cabeça | 8998 | HTTP | &nbsp; | Serviço para executar declarações, empregos e aplicações |
| Jupyter Notebook | Nó de cabeça | 8001 | HTTP | &nbsp; | Site do Jupyter Notebook |

Exemplos:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"` . Neste exemplo, `10.0.0.11` está o endereço IP do headnode que acolhe o serviço Livy.