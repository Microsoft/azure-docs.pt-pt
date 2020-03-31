---
title: Portas utilizadas pelos serviços Hadoop no HDInsight - Azure
description: Este artigo fornece uma lista de portas utilizadas pelos serviços Apache Hadoop em funcionamento no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 67cafbb7934381cd4c2936d6e6dfe7fb19d70735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314696"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portas utilizadas pelos serviços Apache Hadoop no HDInsight

Este documento fornece uma lista das portas utilizadas pelos serviços Apache Hadoop em execução em clusters HDInsight. Também fornece informações sobre as portas utilizadas para se ligar ao cluster utilizando sSH.

## <a name="public-ports-vs-non-public-ports"></a>Portos públicos vs. portos não públicos

Os clusters HDInsight baseados em Linux só expõem três portas publicamente na internet; 22, 23 e 443. Estas portas são utilizadas para aceder de forma segura ao cluster utilizando SSH e serviços expostos sobre o protocolo HTTPS seguro.

Internamente, o HDInsight é implementado por várias Máquinas Virtuais Azure (os nós dentro do cluster) que executam uma Rede Virtual Azure. A partir da rede virtual, pode aceder a portas não expostas através da internet. Por exemplo, se ligar a um dos nós da cabeça usando SSH, a partir do nó de cabeça pode aceder diretamente aos serviços que correm nos nós do cluster.

> [!IMPORTANT]  
> Se não especificar uma Rede Virtual Azure como uma opção de configuração para o HDInsight, uma é criada automaticamente. No entanto, não pode juntar outras máquinas (como outras Máquinas Virtuais Azure ou a sua máquina de desenvolvimento de clientes) a esta rede virtual.

Para juntar máquinas adicionais à rede virtual, tem de criar a rede virtual primeiro e, em seguida, especificá-la ao criar o seu cluster HDInsight. Para mais informações, consulte [Plan a virtual network for HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Portos públicos

Todos os nós de um cluster HDInsight estão localizados numa Rede Virtual Azure, e não podem ser diretamente acedidos a partir da internet. Uma porta de entrada pública fornece acesso à Internet às seguintes portas, que são comuns em todos os tipos de cluster HDInsight.

| Serviço | Porta | Protocolo | Descrição |
| --- | --- | --- | --- |
| sshd |22 |SSH |Liga os clientes ao sshd no nódoado primário. Para mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Liga os clientes ao sshd no nó da borda. Para mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Liga os clientes ao sshd no nódeo secundário. Para mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambari web UI. Ver [Gerir o HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Ver [Gerir o HDInsight utilizando a API De REPOUSO Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Ver [Utilizar mapeiaReduzir com caracol](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Liga-se à Colmeia usando o ODBC. Consulte [O Connect Excel ao HDInsight com o controlador Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Liga-se ao ApacheHive utilizando o JDBC. Consulte [a Ligação à Colmeia Apache no HDInsight utilizando o condutor Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Estão disponíveis os seguintes tipos de cluster específicos:

| Serviço | Porta | Protocolo | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. Ver [Get começou a usar Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Ver [Submeter trabalhos apache spark remotamente usando Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Servidor Spark Thrift |443 |HTTPS |Spark |O servidor Spark Thrift usado para submeter consultas da Hive. Ver [Use Beeline com Apache Hive no HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |UI da teia de tempestade. Consulte [a implementação e gerencie as topoologias da Tempestade Apache no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Procuração kafka descanso |443 |HTTPS |Kafka |Kafka REST API. Veja [interagir com os clusters Apache Kafka em Azure HDInsight usando um proxy REST](kafka/rest-proxy.md) |

### <a name="authentication"></a>Autenticação

Todos os serviços expostos publicamente na internet devem ser autenticados:

| Porta | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de utilizador do SSH especificadas durante a criação do cluster |
| 443 |O nome de login (padrão: administrador) e senha que foram definidas durante a criação do cluster |

## <a name="non-public-ports"></a>Portos não públicos

> [!NOTE]  
> Alguns serviços só estão disponíveis em tipos específicos de cluster. Por exemplo, o HBase só está disponível nos tipos de cluster HBase.

> [!IMPORTANT]  
> Alguns serviços só funcionam com um nódeo de cada vez. Se tentar ligar-se ao serviço no nó principal e receber um erro, tente novamente utilizando o nódoo secundário.

### <a name="ambari"></a>Ambari

| Serviço | Nós | Porta | Caminho de URL | Protocolo |
| --- | --- | --- | --- | --- |
| Ambari web UI | Nódosos da cabeça | 8080 | / | HTTP |
| Ambari REST API | Nódosos da cabeça | 8080 | /api/v1 | HTTP |

Exemplos:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Portas HDFS

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| NameNode web UI |Nódosos da cabeça |30070 |HTTPS |UI web para ver o estado |
| Serviço de metadados NameNode |narizes cabeça |8020 |IPC |Metadados do sistema de ficheiros |
| DataNode |Todos os nós operários |30075 |HTTPS |Web UI para visualizar o estado, registos, etc. |
| DataNode |Todos os nós operários |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós operários |30020 |IPC |Operações de metadados |
| Nó secundário |Nódosos da cabeça |50090 |HTTP |Checkpoint para metadados NameNode |

### <a name="yarn-ports"></a>Portas YARN

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| UI web gestor de recursos |Nódosos da cabeça |8088 |HTTP |UI Web para Gestor de Recursos |
| UI web gestor de recursos |Nódosos da cabeça |8090 |HTTPS |UI Web para Gestor de Recursos |
| Interface de administração do Gestor de Recursos |narizes cabeça |8141 |IPC |Para submissões de aplicações (Hive, servidor hive, porco, etc.) |
| Programador de Gestor de Recursos |narizes cabeça |8030 |HTTP |Interface administrativa |
| Interface de aplicação do Gestor de Recursos |narizes cabeça |8050 |HTTP |Endereço da interface gestor de aplicações |
| NodeManager |Todos os nós operários |30050 |&nbsp; |O endereço do gestor do contentor |
| NodeManager web UI |Todos os nós operários |30060 |HTTP |Interface gestor de recursos |
| Endereço de linha do tempo |Nódosos da cabeça |10200 |RPC |O serviço De linha do tempo RPC. |
| UI web da linha do tempo |Nódosos da cabeça |8188 |HTTP |A Web De serviço timeline UI |

### <a name="hive-ports"></a>Portos colmeias

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nódosos da cabeça |10001 |Thrift |Serviço de ligação à Colmeia (Thrift/JDBC) |
| Metastore do Hive |Nódosos da cabeça |9083 |Thrift |Serviço de ligação aos metadados da Colmeia (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Portas WebHCat

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor WebHCat |Nódosos da cabeça |30111 |HTTP |Web API em cima de HCatalog e outros serviços Hadoop |

### <a name="mapreduce-ports"></a>MapReduzir portas

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| História do Emprego |Nódosos da cabeça |19888 |HTTP |MapReduce JobHistory web UI |
| História do Emprego |Nódosos da cabeça |10020 |&nbsp; |MapReduce JobHistory servidor |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfere saídas intermédias do Mapa para solicitar redutores |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor Oozie |Nódosos da cabeça |11000 |HTTP |URL para o serviço Oozie |
| Servidor Oozie |Nódosos da cabeça |11001 |HTTP |Porto para administrador de Oozie |

### <a name="ambari-metrics"></a>Métricas do Ambari

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| TimeLine (Histórico de aplicações) |Nódosos da cabeça |6188 |HTTP |A Web de serviço TimeLine UI |
| TimeLine (Histórico de aplicações) |Nódosos da cabeça |30200 |RPC |A Web de serviço TimeLine UI |

### <a name="hbase-ports"></a>Portas HBase

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nódosos da cabeça |16000 |&nbsp; |&nbsp; |
| HMaster info Web UI |Nódosos da cabeça |16010 |HTTP |A porta para a HBase Master web UI |
| Servidor da região |Todos os nós operários |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes usam para ligar ao ZooKeeper |

### <a name="kafka-ports"></a>Portos de Kafka

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Corretor |Nódosos operários |9092 |[Protocolo de Arame Kafka](https://kafka.apache.org/protocol.html) |Usado para comunicação com clientes |
| &nbsp; |Nódoa zookeeper |2181 |&nbsp; |A porta que os clientes usam para ligar ao Zookeeper |
| Procuração DE REPOUSO | Nódeos de gestão de Kafka |9400 |HTTPS |[Especificação kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Portos de faíscas

| Serviço | Nós | Porta | Protocolo | Caminho de URL | Descrição |
| --- | --- | --- | --- | --- | --- |
| Servidores Spark Thrift |Nódosos da cabeça |10002 |Thrift | &nbsp; | Serviço de ligação à Spark SQL (Thrift/JDBC) |
| Servidor Livy | Nódosos da cabeça | 8998 | HTTP | &nbsp; | Serviço para executar declarações, empregos e aplicações |
| Bloco de notas do Jupyter | Nódosos da cabeça | 8001 | HTTP | &nbsp; | Site do caderno jupyter |

Exemplos:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. Neste exemplo, `10.0.0.11` é o endereço IP do headnode que acolhe o serviço Livy.
