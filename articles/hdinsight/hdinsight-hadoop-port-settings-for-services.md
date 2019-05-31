---
title: Portas utilizadas pelo serviços do Hadoop no HDInsight - Azure
description: Uma lista das portas utilizadas pelo serviços do Hadoop em execução no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 16041d2b7a971c9ba479c133261930b38d130792
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252788"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portas utilizadas pelos serviços do Apache Hadoop no HDInsight

Este documento fornece uma lista das portas utilizadas pelos serviços de Apache Hadoop em execução em clusters do HDInsight baseado em Linux. Também fornece informações sobre as portas utilizadas para ligar ao cluster através de SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portas públicas versus portas não público

Clusters do HDInsight baseado em Linux expõem somente três portas publicamente na internet; 22, 23 e 443. Estas portas são utilizadas para aceder de forma segura o cluster de utilização do SSH e serviços expostos através do protocolo HTTPS seguro.

Internamente, o HDInsight é implementado por várias máquinas virtuais do Azure (nós dentro do cluster) em execução numa rede Virtual do Azure. De dentro da rede virtual, pode aceder a portas não são expostas através da internet. Por exemplo, se ligar a um de nós principais, através de SSH, do nó principal pode, em seguida, aceder diretamente serviços em execução em nós do cluster.

> [!IMPORTANT]  
> Se não especificar uma rede Virtual do Azure como uma opção de configuração para o HDInsight, um é criado automaticamente. No entanto, não é possível associar outros computadores (como outras máquinas virtuais do Azure ou no seu computador de desenvolvimento do cliente) a esta rede virtual.

Para associar máquinas adicionais para a rede virtual, tem de criar a rede virtual primeiro e, em seguida, especifique-o quando criar o cluster do HDInsight. Para obter mais informações, consulte [capacidades de estender o HDInsight, com uma rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Portas públicas

Todos os nós num cluster do HDInsight estão localizados numa rede Virtual do Azure e não podem ser acedidos diretamente a partir da internet. Um gateway público fornece acesso à internet para as seguintes portas que são comuns em todos os tipos de cluster do HDInsight.

| Serviço | Port | Protocol | Descrição |
| --- | --- | --- | --- |
| sshd |22 |SSH |Liga-se os clientes para sshd no nó principal primário. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Liga-se os clientes para sshd no nó de extremidade. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Liga-se os clientes para sshd no nó principal secundário. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |IU web do Ambari. Consulte [gerir o HDInsight com a IU do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API de REST do Ambari. Consulte [gerir o HDInsight com a API de REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API de REST do HCatalog. Ver [utilizar o Apache Hive com o Curl](hadoop/apache-hadoop-use-pig-curl.md), [utilizar o Apache Pig com o Curl](hadoop/apache-hadoop-use-pig-curl.md), [utilizar o MapReduce com Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Liga-se usar o ODBC do Hive. Ver [ligar o Excel ao HDInsight com o controlador Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Liga ao ApacheHive usando o JDBC. Consulte [ligar ao Apache Hive no HDInsight com o controlador JDBC do Hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Seguem-se disponível para tipos de clusters específicos:

| Serviço | Port | Protocol | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |API de REST de HBase. Consulte [começar a utilizar o Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API de REST do Spark. Consulte [das tarefas de submeter o Apache Spark remotamente com o Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Servidor Spark Thrift |443 |HTTPS |Spark |Servidor Spark Thrift utilizado para submeter consultas do Hive. Consulte [utilizar Beeline com o Apache Hive no HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |IU da web Storm. Consulte [implementar e gerir topologias do Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Autenticação

Todos os serviços expostos publicamente na internet têm de ser autenticados:

| Port | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de utilizador SSH especificadas durante a criação do cluster |
| 443 |O nome de início de sessão (predefinição: administrador) e palavra-passe que foram definidas durante a criação do cluster |

## <a name="non-public-ports"></a>Portas não público

> [!NOTE]  
> Alguns serviços só estão disponíveis em tipos de clusters específicos. Por exemplo, o HBase só está disponível em tipos de clusters de HBase.

> [!IMPORTANT]  
> Alguns serviços executam apenas no nó um principal de cada vez. Se tentar ligar ao serviço no nó principal primário e receber um erro, tente novamente utilizando o nó principal secundário.

### <a name="ambari"></a>Ambari

| Serviço | Nós | Port | Caminho do URL | Protocol | 
| --- | --- | --- | --- | --- |
| IU web do Ambari | Nós de cabeça | 8080 | / | HTTP |
| API REST do Ambari | Nós de cabeça | 8080 | /api/v1 | HTTP |

Exemplos:

* API de REST do Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Portas do HDFS

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| IU da web do NameNode |Nós de cabeça |30070 |HTTPS |Web da interface do Usuário para ver o Estado |
| Serviço de metadados de NameNode |Nós principais |8020 |IPC |Metadados do sistema de ficheiros |
| DataNode |Todos os nós de trabalho |30075 |HTTPS |IU da Web para ver o estado, os logs, etc. |
| DataNode |Todos os nós de trabalho |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós de trabalho |30020 |IPC |Operações de metadados |
| NameNode secundário |Nós de cabeça |50090 |HTTP |Ponto de verificação para os metadados de NameNode |

### <a name="yarn-ports"></a>Portas YARN

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| IU da web do Gestor de recursos |Nós de cabeça |8088 |HTTP |Web da interface do Usuário para o Resource Manager |
| IU da web do Gestor de recursos |Nós de cabeça |8090 |HTTPS |Web da interface do Usuário para o Resource Manager |
| Interface de administração do Gestor de recursos |Nós principais |8141 |IPC |Para envios de aplicativo (Hive, servidor Hive, Pig, etc.) |
| Scheduler do Gestor de recursos |Nós principais |8030 |HTTP |Interface administrativa |
| Interface de aplicativo do Resource Manager |Nós principais |8050 |HTTP |Endereço da interface do Gestor de aplicações |
| NodeManager |Todos os nós de trabalho |30050 |&nbsp; |O endereço do Gestor de contentor |
| IU da web do NodeManager |Todos os nós de trabalho |30060 |HTTP |Interface do Gestor de recursos |
| Endereço de linha cronológica |Nós de cabeça |10200 |RPC |O serviço RPC de serviço de linha do tempo. |
| Linha cronológica web da interface do Usuário |Nós de cabeça |8181 |HTTP |A web do serviço de linha cronológica da interface do Usuário |

### <a name="hive-ports"></a>Portas do Hive

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nós de cabeça |10001 |Thrift |Serviço para ligar ao Hive (Thrift/JDBC) |
| Metastore do Hive |Nós de cabeça |9083 |Thrift |Serviço para ligar aos metadados do Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Portas de WebHCat

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| Servidor de WebHCat |Nós de cabeça |30111 |HTTP |API Web com base no HCatalog e outros serviços do Hadoop |

### <a name="mapreduce-ports"></a>Portas de MapReduce

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| JobHistory |Nós de cabeça |19888 |HTTP |IU da web do MapReduce JobHistory |
| JobHistory |Nós de cabeça |10020 |&nbsp; |Servidor de MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Mapa de intermediário de transferências produz para pedir redutores |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| Servidor de Oozie |Nós de cabeça |11000 |HTTP |URL para o serviço de Oozie |
| Servidor de Oozie |Nós de cabeça |11001 |HTTP |Porta para o administrador de Oozie |

### <a name="ambari-metrics"></a>Métricas do Ambari

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| Linha cronológica (histórico da aplicação) |Nós de cabeça |6188 |HTTP |A web do serviço de linha cronológica da interface do Usuário |
| Linha cronológica (histórico da aplicação) |Nós de cabeça |30200 |RPC |A web do serviço de linha cronológica da interface do Usuário |

### <a name="hbase-ports"></a>Portas de HBase

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nós de cabeça |16000 |&nbsp; |&nbsp; |
| Informações de HMaster IU da Web |Nós de cabeça |16010 |HTTP |A porta para a interface do Usuário de web do principal do HBase |
| Servidor de região |Todos os nós de trabalho |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes utilizam para ligar ao ZooKeeper |

### <a name="kafka-ports"></a>Portas de Kafka

| Serviço | Nós | Port | Protocol | Descrição |
| --- | --- | --- | --- | --- |
| Broker |Nós de trabalho |9092 |[Protocolo de Kafka](https://kafka.apache.org/protocol.html) |Utilizado para comunicação de cliente |
| &nbsp; |Nós do Zookeeper |2181 |&nbsp; |A porta que os clientes utilizam para ligar ao Zookeeper |

### <a name="spark-ports"></a>Portas de Spark

| Serviço | Nós | Port | Protocol | Caminho do URL | Descrição |
| --- | --- | --- | --- | --- | --- |
| Servidores de Spark Thrift |Nós de cabeça |10002 |Thrift | &nbsp; | Serviço para ligar ao Spark SQL (Thrift/JDBC) |
| Servidor do Livy | Nós de cabeça | 8998 | HTTP | &nbsp; | Serviço de demonstrativos, tarefas e aplicações em execução |
| Jupyter Notebook | Nós de cabeça | 8001 | HTTP | &nbsp; | Web site de bloco de notas do Jupyter |

Exemplos:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. Neste exemplo, `10.0.0.11` é o endereço IP do nó principal que aloja o serviço do Livy.
