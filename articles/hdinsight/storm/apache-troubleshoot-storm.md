---
title: Tempestade de resolução de problemas usando Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre a utilização de Apache Storm com Azure HDInsight.
keywords: Azure HDInsight, Tempestade, FAQ, guia de resolução de problemas, problemas comuns
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: c81084c77b355a5d60c72564c58a98e08da14312
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946309"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Resolução de problemas Tempestade Apache usando Azure HDInsight

Conheça as principais questões e as suas resoluções para trabalhar com [cargas apache storm](https://storm.apache.org/) em [Apache Ambari.](https://ambari.apache.org/)

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como acesso à UI da tempestade num aglomerado?

Você tem duas opções para aceder ao UI storm a partir de um navegador:

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. Vai ao painel Ambari.
2. Na lista de serviços, selecione **Storm**.
3. No menu **Links Rápidos,** selecione **Storm UI**.

### <a name="direct-link"></a>Ligação direta

Pode aceder à UI da tempestade no seguinte URL:

`https://<cluster DNS name>/stormui`

Exemplo: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como transfira informação do centro de verificação do storm event de uma topologia para outra?

Quando desenvolver topologias que lêem a partir de Azure Event Hubs utilizando o hub de eventos HDInsight Storm .jar ficheiro, deve implementar uma topologia com o mesmo nome num novo cluster. No entanto, deve reter os dados de verificação que foram comprometidos com o [Apache ZooKeeper](https://zookeeper.apache.org/) no antigo cluster.

### <a name="where-checkpoint-data-is-stored"></a>Onde os dados do checkpoint são armazenados

Os dados do checkpoint para compensações são armazenados pelo centro de eventos no ZooKeeper em dois caminhos de raiz:

- Os pontos de verificação não transações são armazenados em `/eventhubspout` .

- Os dados do ponto de verificação do bico de transação são armazenados em `/transactional` .

### <a name="how-to-restore"></a>Como restaurar

Para obter os scripts e bibliotecas que usa para exportar dados do ZooKeeper e, em seguida, importar os dados de volta para o ZooKeeper com um novo nome, consulte [exemplos de HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta lib tem .jar ficheiros que contenham a implementação para a operação de exportação/importação. A pasta bash tem um script de exemplo que demonstra como exportar dados do servidor ZooKeeper no antigo cluster, e depois importá-lo de volta para o servidor ZooKeeper no novo cluster.

Executar [o](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) stormmeta.sh script dos nós do ZooKeeper para exportar e, em seguida, importar dados. Atualize o script para a versão correta da Plataforma de Dados de Hortonworks (HDP). (Estamos a trabalhar em tornar estes scripts genéricos em HDInsight. Os scripts genéricos podem ser executados a partir de qualquer nó no cluster sem modificações por parte do utilizador.)

O comando de exportação escreve os metadados para um caminho do Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS) (em Azure Blob Storage ou Azure Data Lake Storage) num local que definiu.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Metadados de compensação de exportação

1. Utilize o SSH para ir ao cluster ZooKeeper no cluster a partir do qual a compensação do ponto de verificação precisa de ser exportada.
2. Executar o seguinte comando (depois de atualizar a cadeia de versão HDP) para exportar dados do ZooKeeper para a `/stormmetadta/zkdata` trajetória HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importar metadados compensados

1. Utilize o SSH para ir ao cluster ZooKeeper no cluster a partir do qual a compensação do ponto de verificação precisa de ser importada.
2. Executar o seguinte comando (depois de atualizar a cadeia de versão HDP) para importar os dados do ZooKeeper compensando os dados da trajetória HDFS `/stormmetadata/zkdata` para o servidor ZooKeeper no cluster alvo:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Elimine os metadados de compensação para que as topologias possam começar a processar dados desde o início, ou a partir de um tempotando que o utilizador escolher

1. Utilize o SSH para ir ao cluster ZooKeeper no cluster a partir do qual a compensação do ponto de verificação precisa de ser eliminada.
2. Executar o seguinte comando (depois de atualizar a cadeia de versão HDP) para eliminar todos os dados de compensação do ZooKeeper no cluster atual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como localizo binários de tempestade num aglomerado?

Os binários de tempestade para a atual pilha de HDP estão em `/usr/hdp/current/storm-client` . A localização é a mesma tanto para os nós da cabeça como para os nós dos trabalhadores.

Pode haver vários binários para versões específicas do HDP em /usr/hdp (por exemplo, `/usr/hdp/2.5.0.1233/storm` ). A `/usr/hdp/current/storm-client` pasta está sisliged para a versão mais recente que está a ser executada no cluster.

Para obter mais informações, consulte [Connect to a HDInsight cluster utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) e [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como determino a topologia de implantação de um aglomerado de tempestades?

Em primeiro lugar, identifique todos os componentes instalados com a Tempestade HDInsight. Um aglomerado de tempestades é composto por quatro categorias de nó:

* Nó de gateway
* Nó de cabeça
* Nómada do ZooKeeper
* Nódoas operárias

### <a name="gateway-nodes"></a>Nó de gateway

Um nó de gateway é um serviço de procuração inversa e de porta de entrada que permite o acesso do público a um serviço de gestão Ambari ativo. Também trata da eleição do líder Ambari.

### <a name="head-nodes"></a>Nó de cabeça

Os nódes de cabeça de tempestade executam os seguintes serviços:
* Nimbus
* Servidor Ambari
* Servidor Ambari Metrics
* Colecionador de Métricas Ambari
 
### <a name="zookeeper-nodes"></a>Nómada do ZooKeeper

HDInsight vem com um quórum zookeeper de três nós. O tamanho do quórum é fixo e não pode ser reconfigurado.

Os serviços de tempestade no cluster são configurados para utilizar automaticamente o quórum ZooKeeper.

### <a name="worker-nodes"></a>Nódoas operárias

Os nóns dos trabalhadores da tempestade gerem os seguintes serviços:
* Supervisor
* Máquinas virtuais Java trabalhadoras (JVMs), para executar toplogies
* Agente Ambari

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Como localizo binaários storm hub para desenvolvimento?

Para obter mais informações sobre a utilização do hub de eventos Storm .jar ficheiros com a sua topologia, consulte os seguintes recursos.

### <a name="java-based-topology"></a>Topologia baseada em Java

[Eventos de processo de Azure Event Hubs com Apache Storm em HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia baseada em C #(Mono on HDInsight 3.4+ Linux Storm clusters)

[Eventos de processo de Azure Event Hubs com Apache Storm on HDInsight (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Mais recentes binaries do hub de eventos da Tempestade Apache para hdInsight 3.5+ Clusters de tempestade Linux

Para aprender a usar o mais recente bico do hub de eventos storm que funciona com clusters hdInsight 3.5+ Linux Storm, consulte o [ficheiro mvn-repo readme](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Exemplos de código fonte

Veja [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) de como ler e escrever a partir do Azure Event Hub usando uma topologia da Tempestade Apache (escrita em Java) num cluster Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Como localizo ficheiros de configuração Storm Log4J 2 em clusters?

Para identificar ficheiros de configuração [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para serviços storm.

### <a name="on-head-nodes"></a>Nos acenos de cabeça

A configuração Nimbus Log4J é lida a partir de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

### <a name="on-worker-nodes"></a>Nos acenos dos trabalhadores

A configuração log4J do supervisor é lida a partir de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

O ficheiro de configuração Log4J do trabalhador é lido a partir de `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` .

Exemplos: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Não é uma exceção de líder

Ao submeter uma topologia, o utilizador pode receber uma mensagem de erro semelhante a: `Topology submission exception, cause not a leader, the current leader is NimbusInfo` .

Para resolver, o utilizador poderá ter de arquivar um bilhete para reiniciar/reiniciar os nós. Para obter mais informações, veja [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).