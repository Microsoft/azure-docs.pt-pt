---
title: Resolver problemas de Storm através da utilização do Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre como utilizar o Apache Storm com o Azure HDInsight.
keywords: FAQ do Azure HDInsight, Storm, guia, problemas comuns de resolução de problemas
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 3866e25cc3c87f569e84b2d5639d25aa9386cc78
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713750"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Resolver problemas relacionados com o Apache Storm através da utilização do Azure HDInsight

Saiba mais sobre os principais problemas e resolução para trabalhar com [Apache Storm](https://storm.apache.org/) payloads na [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como posso aceder a IU do Storm num cluster?
Tem duas opções para aceder a IU do Storm a partir de um browser:

### <a name="ambari-ui"></a>IU do Ambari
1. Vá para o dashboard do Ambari.
2. Na lista de serviços, selecione **Storm**.
3. Na **ligações rápidas** menu, selecione **IU do Storm**.

### <a name="direct-link"></a>Ligação direta
Pode acessar a IU do Storm no seguinte URL:

https://\<nome DNS do cluster\>/stormui

Exemplo:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como transferir as informações de ponto de verificação de spout de hub de eventos de Storm de uma topologia para outra?

Ao desenvolver topologias que leem os Hubs de eventos do Azure ao utilizar o hub de eventos do Storm do HDInsight spout ficheiro. JAR, tem de implementar uma topologia que tem o mesmo nome num novo cluster. No entanto, tem de manter os dados de ponto de verificação que foi consolidados [Apache ZooKeeper](https://zookeeper.apache.org/) no cluster antigo.

### <a name="where-checkpoint-data-is-stored"></a>Onde estão armazenados os dados de ponto de verificação
Dados de ponto de verificação para deslocamentos são armazenados pelo spout do hub de eventos no ZooKeeper em dois caminhos de raiz:
- Pontos de verificação de spout de não transacional são armazenados em /eventhubspout.
- Dados de ponto de verificação de spout transacional são armazenados na / transacional.

### <a name="how-to-restore"></a>Como restaurar
Para obter os scripts e as bibliotecas que utilizar para exportar os dados fora do ZooKeeper e, em seguida, importar os dados de volta ao ZooKeeper com um novo nome, consulte [exemplos de Storm do HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta lib tem ficheiros. JAR que contêm a implementação para a operação de exportação/importação. A pasta de bash tem um script de exemplo que demonstra como exportar os dados do servidor ZooKeeper no cluster antigo e, em seguida, importá-lo novamente para o servidor ZooKeeper no novo cluster.

Executar o [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) script a partir de nós do ZooKeeper para exportar e, em seguida, importar dados. Atualize o script para a versão correta do Hortonworks Data Platform (HDP). (Estamos a trabalhar em tornar esses scripts genérico do HDInsight. Scripts genéricas podem executar partir de qualquer nó no cluster sem modificações, o usuário.)

O comando de exportação escreve os metadados para um caminho de Apache Hadoop Distributed File System (HDFS) (em armazenamento de Blobs do Azure ou no armazenamento do Azure Data Lake) num local que definir.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Exportar metadados de deslocamento
1. Utilize o SSH para aceder ao cluster do ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser exportado.
2. Execute o seguinte comando (depois de atualizar a cadeia de versão HDP) para exportar dados de deslocamento do ZooKeeper para o caminho do HDFS /stormmetadta/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importar metadados de deslocamento
1. Utilize o SSH para aceder ao cluster do ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser importado.
2. Execute o seguinte comando (depois de atualizar a cadeia de versão HDP) para importar dados de deslocamento do ZooKeeper do /stormmetadata/zkdata de caminho do HDFS para o servidor ZooKeeper no cluster de destino:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Eliminar os metadados de deslocamento para que topologias podem começar a processar os dados, desde o início ou a partir de um carimbo que o utilizador escolhe
1. Utilize o SSH para aceder ao cluster do ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser eliminado.
2. Execute o seguinte comando (depois de atualizar a cadeia de versão HDP) para eliminar todos os dados de deslocamento de ZooKeeper no cluster atual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como faço para localizar os binários do Storm num cluster?
Os binários do Storm para a pilha HDP atual estão em /usr/hdp/current/storm-client. A localização é o mesmo para nós principais e nós de trabalho.
 
Podem existir vários binários para versões específicas de HDP em /usr/hdp (por exemplo, /usr/hdp/2.5.0.1233/storm). A pasta de /usr/hdp/current/storm-client é symlinked para a versão mais recente que está em execução no cluster.

Para obter mais informações, consulte [ligar a um cluster do HDInsight ao utilizar SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Apache Storm](https://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como posso determinar a topologia de implementação de um cluster do Storm?
Em primeiro lugar, identifique todos os componentes que são instalados com o HDInsight Storm. Um cluster do Storm consiste em quatro categorias de nó:

* Nós de gateway
* Nós de cabeça
* Nós do zooKeeper
* Nós de trabalho
 
### <a name="gateway-nodes"></a>Nós de gateway
Um nó de gateway é um gateway e o serviço de proxy inverso que permite o acesso público a um serviço de gestão do Active Directory Ambari. Ele também faz a eleição de coordenador do Ambari.
 
### <a name="head-nodes"></a>Nós de cabeça
Nós principais do Storm, execute os seguintes serviços:
* Nimbus
* Servidor do Ambari
* Servidor de métricas do Ambari
* Recoletor de métricas do Ambari
 
### <a name="zookeeper-nodes"></a>Nós do zooKeeper
HDInsight vem com um quórum ZooKeeper de três nós. O tamanho de quórum é fixa e não pode ser reconfigurado.
 
Serviços de Storm no cluster são configurados para utilizar automaticamente o quórum ZooKeeper.
 
### <a name="worker-nodes"></a>Nós de trabalho
Nós de trabalho do Storm, execute os seguintes serviços:
* Supervisor
* Função de trabalho máquinas virtuais Java (JVMs), para a execução de topologias
* Agente do Ambari
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Como faço para localizar binários spout de hub de eventos do Storm para desenvolvimento?
 
Para obter mais informações sobre como utilizar ficheiros. JAR do spout do Storm event hub com a topologia, consulte os seguintes recursos.
 
### <a name="java-based-topology"></a>Topologia baseada em Java
[Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-com base topologia (Mono em clusters do Storm de Linux do HDInsight 3.4 +)
[Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Binários para clusters do Storm de Linux do HDInsight 3.5 + de spout do hub de eventos mais recentes do Apache Storm
Para saber como utilizar a mais recente Storm event hub spout que funciona com clusters do Storm do HDInsight 3.5 + Linux, veja o repositório do arquétipo [ficheiro Leia-me](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Exemplos de código de origem
Ver [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) como ler e escrever a partir do Hub de eventos do Azure com uma topologia de Apache Storm (escrita em Java) num cluster HDInsight do Azure.
 
## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Como faço para localizar ficheiros de configuração do Storm Log4J 2 em clusters?
 
Para identificar [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) ficheiros de configuração para os serviços do Storm.
 
### <a name="on-head-nodes"></a>Em nós principais
A configuração Log4J do Nimbus é lidos a partir /usr/hdp/\<versão HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Em nós de trabalho
A configuração Log4J do supervisor é lidos a partir /usr/hdp/\<versão HDP\>/storm/log4j2/cluster.xml.
 
O ficheiro de configuração Log4J do trabalho é lidos a partir /usr/hdp/\<versão HDP\>/storm/log4j2/worker.xml.
 
Exemplos: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Consultar Também
[Resolver problemas com o Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
