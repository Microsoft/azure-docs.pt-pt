---
title: Resolver problemas de Storm através da utilização do Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre como utilizar o Apache Storm com o Azure HDInsight.
keywords: FAQ do Azure HDInsight, Storm, guia, problemas comuns de resolução de problemas
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271931"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Resolver problemas relacionados com o Apache Storm através da utilização do Azure HDInsight

Conheça os principais problemas e as suas resoluções para trabalhar com as cargas da [Tempestade Apache](https://storm.apache.org/) em [Apache Ambari.](https://ambari.apache.org/)

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como posso aceder a IU do Storm num cluster?

Tem duas opções para aceder a IU do Storm a partir de um browser:

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. Vá para o dashboard do Ambari.
2. Na lista de serviços, selecione **Storm**.
3. No menu **Quick Links,** selecione **Storm UI**.

### <a name="direct-link"></a>Ligação direta

Pode acessar a IU do Storm no seguinte URL:

`https://<cluster DNS name>/stormui`

Exemplo: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como transferir as informações de ponto de verificação de spout de hub de eventos de Storm de uma topologia para outra?

Ao desenvolver topologias que leem os Hubs de eventos do Azure ao utilizar o hub de eventos do Storm do HDInsight spout ficheiro. JAR, tem de implementar uma topologia que tem o mesmo nome num novo cluster. No entanto, deve reter os dados do checkpoint que foram comprometidos com o [Apache ZooKeeper](https://zookeeper.apache.org/) no antigo aglomerado.

### <a name="where-checkpoint-data-is-stored"></a>Onde estão armazenados os dados de ponto de verificação

Dados de ponto de verificação para deslocamentos são armazenados pelo spout do hub de eventos no ZooKeeper em dois caminhos de raiz:

- Os postos de controlo não transacionais são armazenados em `/eventhubspout`.

- Os dados do checkpoint do bico transacional são armazenados em `/transactional`.

### <a name="how-to-restore"></a>Como restaurar

Para obter os scripts e bibliotecas que utiliza para exportar dados do ZooKeeper e, em seguida, importar os dados de volta para zookeeper com um novo nome, consulte os [exemplos da Tempestade HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta lib tem ficheiros. JAR que contêm a implementação para a operação de exportação/importação. A pasta de bash tem um script de exemplo que demonstra como exportar os dados do servidor ZooKeeper no cluster antigo e, em seguida, importá-lo novamente para o servidor ZooKeeper no novo cluster.

Execute o [roteiro stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) dos nomes do ZooKeeper para exportar e, em seguida, importar dados. Atualize o script para a versão correta do Hortonworks Data Platform (HDP). (Estamos a trabalhar em tornar esses scripts genérico do HDInsight. Scripts genéricas podem executar partir de qualquer nó no cluster sem modificações, o usuário.)

O comando de exportação escreve os metadados para um caminho do Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS) (em Armazenamento de Blob Azure ou Armazenamento de Lago de Dados Azure) num local que definiu.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Exportar metadados de deslocamento

1. Utilize o SSH para aceder ao cluster do ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser exportado.
2. Executar o seguinte comando (depois de atualizar a cadeia da versão HDP) para exportar dados de compensação do ZooKeeper para o caminho `/stormmetadta/zkdata` HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importar metadados de deslocamento

1. Utilize o SSH para aceder ao cluster do ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser importado.
2. Executar o seguinte comando (depois de atualizar a cadeia da versão HDP) para importar dados de compensação do ZooKeeper do caminho HDFS `/stormmetadata/zkdata` para o servidor ZooKeeper no cluster alvo:

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

Binários de tempestade para a atual pilha de HDP estão em `/usr/hdp/current/storm-client`. A localização é o mesmo para nós principais e nós de trabalho.

Pode haver vários binários para versões hdp específicas em /usr/hdp (por exemplo, `/usr/hdp/2.5.0.1233/storm`). A pasta `/usr/hdp/current/storm-client` está sincronizada com a versão mais recente que está a ser recorrida no cluster.

Para mais informações, consulte [Connect to a HDInsight, utilizando SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Apache Storm](https://storm.apache.org/).

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

[Eventos de processo saqueados de Hubs de Eventos Azure com Tempestade Apache no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-com base topologia (Mono em clusters do Storm de Linux do HDInsight 3.4 +)

[Eventos de processo saqueados de Hubs de Eventos Azure com Tempestade Apache no HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Binários para clusters do Storm de Linux do HDInsight 3.5 + de spout do hub de eventos mais recentes do Apache Storm

Para saber como usar o mais recente spout do centro de eventos storm que funciona com os aglomerados hDInsight 3.5+ Linux Storm, consulte o [ficheiro de leitura mvn-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Exemplos de código de origem

Veja [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) de como ler e escrever a partir do Azure Event Hub usando uma topologia de tempestade Apache (escrita em Java) num cluster Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Como faço para localizar ficheiros de configuração do Storm Log4J 2 em clusters?

Para identificar ficheiros de configuração [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para serviços storm.

### <a name="on-head-nodes"></a>Em nós principais

A configuração Nimbus Log4J é lida a partir de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>Em nós de trabalho

A configuração do supervisor Log4J é lida a partir de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

O ficheiro de configuração Log4J do trabalhador é lido a partir de `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Exemplos: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Não é uma exceção de líder.

Ao submeter uma topologia, o utilizador pode receber uma mensagem de erro semelhante a: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

Para resolver, o utilizador poderá ter de arquivar um bilhete para reiniciar os nós/reiniciar. Para mais informações, consulte [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Passos Seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
