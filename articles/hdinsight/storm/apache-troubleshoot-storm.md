---
title: Tempestade de resolução de problemas usando Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre o uso da Tempestade Apache com o Azure HDInsight.
keywords: Azure HDInsight, Storm, FAQ, guia de resolução de problemas, problemas comuns
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271931"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Resolução de problemas Apache Storm usando Azure HDInsight

Conheça os principais problemas e as suas resoluções para trabalhar com as cargas da [Tempestade Apache](https://storm.apache.org/) em [Apache Ambari.](https://ambari.apache.org/)

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como posso aceder à Tempestade UI num aglomerado?

Você tem duas opções para aceder à Tempestade UI a partir de um navegador:

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. Vai ao tablier ambari.
2. Na lista de serviços, selecione **Storm**.
3. No menu **Quick Links,** selecione **Storm UI**.

### <a name="direct-link"></a>Ligação direta

Pode aceder à Tempestade UI no seguinte URL:

`https://<cluster DNS name>/stormui`

Exemplo: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como posso transferir informações do centro de eventos storm de uma topologia para outra?

Quando desenvolve topologias que são lidas a partir de Hubs de Eventos Azure utilizando o ficheiro hDInsight Storm hub .jar, você deve implementar uma topologia que tenha o mesmo nome em um novo cluster. No entanto, deve reter os dados do checkpoint que foram comprometidos com o [Apache ZooKeeper](https://zookeeper.apache.org/) no antigo aglomerado.

### <a name="where-checkpoint-data-is-stored"></a>Onde os dados do checkpoint são armazenados

Os dados do checkpoint para compensações são armazenados pelo hub do evento em ZooKeeper em dois caminhos de raiz:

- Os postos de controlo não transacionais são armazenados em `/eventhubspout`.

- Os dados do ponto de verificação do bico transacional são armazenados em `/transactional`.

### <a name="how-to-restore"></a>Como restaurar

Para obter os scripts e bibliotecas que utiliza para exportar dados do ZooKeeper e, em seguida, importar os dados de volta para zookeeper com um novo nome, consulte os [exemplos da Tempestade HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta lib tem ficheiros .jar que contêm a implementação para a operação de exportação/importação. A pasta bash tem um script de exemplo que demonstra como exportar dados do servidor ZooKeeper no antigo cluster, e depois importá-lo de volta para o servidor ZooKeeper no novo cluster.

Execute o [roteiro stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) dos nomes do ZooKeeper para exportar e, em seguida, importar dados. Atualize o script para a versão correta da Plataforma de Dados Hortonworks (HDP). (Estamos a trabalhar para tornar estes scripts genéricos no HDInsight. Scripts genéricos podem ser executados a partir de qualquer nó no cluster sem modificações pelo utilizador.)

O comando de exportação escreve os metadados para um caminho do Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS) (em Armazenamento de Blob Azure ou Armazenamento de Lago de Dados Azure) num local que definiu.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Metadados de exportação compensados

1. Utilize o SSH para ir ao aglomerado ZooKeeper no cluster a partir do qual o ponto de verificação de compensação precisa de ser exportado.
2. Executar o seguinte comando (depois de atualizar a cadeia da `/stormmetadta/zkdata` versão HDP) para exportar dados de compensação do ZooKeeper para o caminho hdfs:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Metadados de importação compensados

1. Utilize o SSH para ir ao aglomerado ZooKeeper no cluster a partir do qual o ponto de verificação de compensação precisa de ser importado.
2. Executar o seguinte comando (depois de atualizar a cadeia da versão HDP) `/stormmetadata/zkdata` para importar dados de compensação do ZooKeeper do caminho HDFS para o servidor ZooKeeper no cluster alvo:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Eliminar metadados offset para que as topoologias possam começar a processar dados desde o início, ou a partir de uma marca temporal que o utilizador escolher

1. Utilize o SSH para ir ao aglomerado ZooKeeper no cluster a partir do qual o offset do checkpoint precisa de ser eliminado.
2. Executar o seguinte comando (depois de atualizar a cadeia da versão HDP) para eliminar todos os dados de compensação do ZooKeeper no cluster atual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como posso localizar binários storm num aglomerado?

Binários de tempestade para a `/usr/hdp/current/storm-client`atual pilha de HDP estão em . A localização é a mesma tanto para os nós da cabeça como para os nós dos trabalhadores.

Pode haver vários binários para versões hdp específicas `/usr/hdp/2.5.0.1233/storm`em /usr/hdp (por exemplo, ). A `/usr/hdp/current/storm-client` pasta é sincronizada com a versão mais recente que está a ser recorrida no cluster.

Para mais informações, consulte [Connect to a HDInsight, utilizando SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como determino a topoologia de implantação de um aglomerado de tempestades?

Em primeiro lugar, identifique todos os componentes instalados com a Tempestade HDInsight. Um aglomerado de tempestades é composto por quatro categorias de nós:

* Nódosos gateway
* Nódosos da cabeça
* Nódoa zookeeper
* Nódosos operários

### <a name="gateway-nodes"></a>Nódosos gateway

Um nó gateway é um serviço de procuração inversa que permite o acesso do público a um serviço de gestão ambari ativo. Também lida com a eleição do líder Ambari.

### <a name="head-nodes"></a>Nódosos da cabeça

Os nódosos da cabeça da tempestade executam os seguintes serviços:
* Nimbus
* Servidor Ambari
* Servidor ambari Métricas
* Colecionador de Métricas Ambari
 
### <a name="zookeeper-nodes"></a>Nódoa zookeeper

HDInsight vem com um quórum zookeeper de três nodos. O tamanho do quórum é fixo e não pode ser reconfigurado.

Os serviços de tempestade no cluster estão configurados para utilizar automaticamente o quórum ZooKeeper.

### <a name="worker-nodes"></a>Nódosos operários

Os nódosos dos trabalhadores da tempestade executam os seguintes serviços:
* Supervisor
* Máquinas virtuais Java operárias (JVMs), para correr topoologias
* Agente Ambari

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Como posso localizar os binárias do centro de eventos storm para o desenvolvimento?

Para obter mais informações sobre a utilização do hub de eventos storm spout .jar files com a sua topologia, consulte os seguintes recursos.

### <a name="java-based-topology"></a>Topologia baseada em Java

[Eventos de processo saqueados de Hubs de Eventos Azure com Tempestade Apache no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia baseada em C#(Mono em HDInsight 3.4+ Linux Storm clusters)

[Processe eventos a partir de Hubs de Eventos Azure com Tempestade Apache em HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Os mais recentes aglomerados de tempestade de apache spout para aglomerados de tempestade HDInsight 3.5+ Linux

Para saber como usar o mais recente spout do centro de eventos storm que funciona com os aglomerados hDInsight 3.5+ Linux Storm, consulte o [ficheiro de leitura mvn-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Exemplos de código de origem

Veja [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) de como ler e escrever a partir do Azure Event Hub usando uma topologia de tempestade Apache (escrita em Java) num cluster Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Como localizo ficheiros de configuração Storm Log4J 2 em clusters?

Para identificar ficheiros de configuração [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para serviços storm.

### <a name="on-head-nodes"></a>Nos narizes da cabeça

A configuração Nimbus Log4J é lida a partir de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>Em nódos os trabalhadores

A configuração do supervisor `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`Log4J é lida a partir de .

O ficheiro de configuração Log4J do trabalhador é lido a partir de `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Exemplos:`/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Não é uma exceção de líder.

Ao submeter uma topologia, o utilizador pode `Topology submission exception, cause not a leader, the current leader is NimbusInfo`receber uma mensagem de erro semelhante a: .

Para resolver, o utilizador poderá ter de arquivar um bilhete para reiniciar os nós/reiniciar. Para mais informações, consulte [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
