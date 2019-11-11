---
title: Solucionar problemas do Storm usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como usar o Apache Storm com o Azure HDInsight.
keywords: Azure HDInsight, Storm, perguntas frequentes, guia de solução de problemas, problemas comuns
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: e2cc9dd81a0bbefa5cf37facb6067bda07117eaf
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903727"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Solucionar problemas Apache Storm usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas resoluções para trabalhar com cargas de [Apache Storm](https://storm.apache.org/) no [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como fazer acessar a interface do usuário do Storm em um cluster?

Você tem duas opções para acessar a interface do usuário do Storm por meio de um navegador:

### <a name="apache-ambari-ui"></a>Interface do usuário do Apache Ambari

1. Vá para o painel do Ambari.
2. Na lista de serviços, selecione **Storm**.
3. No menu **links rápidos** , selecione **interface do usuário do Storm**.

### <a name="direct-link"></a>Link direto

Você pode acessar a interface do usuário do Storm na seguinte URL:

`https://<cluster DNS name>/stormui`

Exemplo: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como fazer transferir informações de ponto de verificação Spout do hub de eventos do Storm de uma topologia para outra?

Ao desenvolver topologias que lêem de hubs de eventos do Azure usando o arquivo Spout. jar do hub de eventos Storm do HDInsight, você deve implantar uma topologia que tenha o mesmo nome em um novo cluster. No entanto, você deve reter os dados do ponto de verificação que foram confirmados para [Apache ZooKeeper](https://zookeeper.apache.org/) no cluster antigo.

### <a name="where-checkpoint-data-is-stored"></a>Onde os dados do ponto de verificação são armazenados

Os dados de ponto de verificação para deslocamentos são armazenados pelo Spout do hub de eventos em ZooKeeper em dois caminhos raiz:

- Os pontos de verificação Spout não transacionais são armazenados em `/eventhubspout`.

- Os dados transacionais do ponto de verificação Spout são armazenados em `/transactional`.

### <a name="how-to-restore"></a>Como restaurar

Para obter os scripts e as bibliotecas que você usa para exportar dados de ZooKeeper e, em seguida, importar os dados de volta para ZooKeeper com um novo nome, consulte [exemplos do HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta lib tem arquivos. jar que contêm a implementação para a operação de exportação/importação. A pasta bash tem um script de exemplo que demonstra como exportar dados do servidor ZooKeeper no cluster antigo e, em seguida, importá-los de volta para o servidor ZooKeeper no novo cluster.

Execute o script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) dos nós ZooKeeper para exportar e importar dados. Atualize o script para a versão correta do HDP (Hortonworks Data Platform). (Estamos trabalhando para tornar esses scripts genéricos no HDInsight. Scripts genéricos podem ser executados de qualquer nó no cluster sem modificações pelo usuário.)

O comando Exportar grava os metadados em um caminho Apache Hadoop Sistema de Arquivos Distribuído (HDFS) (no armazenamento de BLOBs do Azure ou Azure Data Lake Storage) em um local definido por você.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Exportar metadados de deslocamento

1. Use SSH para ir para o cluster ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser exportado.
2. Execute o comando a seguir (depois de atualizar a cadeia de caracteres de versão do HDP) para exportar os dados de deslocamento ZooKeeper para o caminho `/stormmetadta/zkdata` HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importar metadados de deslocamento

1. Use SSH para ir para o cluster ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser importado.
2. Execute o comando a seguir (depois de atualizar a cadeia de caracteres de versão do HDP) para importar os dados de deslocamento ZooKeeper do caminho HDFS `/stormmetadata/zkdata` para o servidor ZooKeeper no cluster de destino:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Excluir metadados de deslocamento para que as topologias possam iniciar o processamento de dados desde o início ou de um carimbo de data/hora escolhido pelo usuário

1. Use SSH para ir para o cluster ZooKeeper no cluster do qual o deslocamento do ponto de verificação precisa ser excluído.
2. Execute o seguinte comando (depois de atualizar a cadeia de caracteres de versão do HDP) para excluir todos os dados de deslocamento ZooKeeper no cluster atual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como fazer localizar binários do Storm em um cluster?

Os binários do Storm para a pilha HDP atual estão em `/usr/hdp/current/storm-client`. O local é o mesmo para nós de cabeçalho e para nós de trabalho.

Pode haver vários binários para versões específicas do HDP em/usr/HDP (por exemplo, `/usr/hdp/2.5.0.1233/storm`). A pasta `/usr/hdp/current/storm-client` é symlinked para a versão mais recente que está em execução no cluster.

Para obter mais informações, consulte [conectar-se a um cluster HDInsight usando SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como fazer determinar a topologia de implantação de um cluster Storm?

Primeiro, identifique todos os componentes instalados com o HDInsight Storm. Um cluster Storm consiste em quatro categorias de nó:

* Nós de gateway
* Nós de cabeça
* Nós ZooKeeper
* Nós de trabalho

### <a name="gateway-nodes"></a>Nós de gateway

Um nó de gateway é um gateway e um serviço de proxy reverso que habilita o acesso público a um serviço de gerenciamento de Ambari ativo. Ele também lida com eleição de líder Ambari.

### <a name="head-nodes"></a>Nós de cabeça

Nós de cabeçalho do Storm executam os seguintes serviços:
* Nimbus
* Servidor Ambari
* Servidor de métricas Ambari
* Coletor de métricas do Ambari
 
### <a name="zookeeper-nodes"></a>Nós ZooKeeper

O HDInsight vem com um quorum de ZooKeeper de três nós. O tamanho do quorum é fixo e não pode ser reconfigurado.

Os serviços do Storm no cluster são configurados para usar automaticamente o quorum ZooKeeper.

### <a name="worker-nodes"></a>Nós de trabalho

Os nós de trabalho do Storm executam os seguintes serviços:
* Supervisor
* Máquinas virtuais Java de trabalho (JVMs) para topologias em execução
* Agente Ambari

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Como fazer localizar binários Spout do hub de eventos Storm para desenvolvimento?

Para obter mais informações sobre como usar arquivos. jar Spout do hub de eventos do Storm com sua topologia, consulte os recursos a seguir.

### <a name="java-based-topology"></a>Topologia baseada em Java

[Processar eventos dos hubs de eventos do Azure com o Apache Storm no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#topologia baseada em (mono no HDInsight 3.4 + clusters Storm do Linux)

[Processar eventos dos hubs de eventos do Azure com o Apache StormC#no HDInsight ()](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Mais recentes Apache Storm binários Spout do hub de eventos para clusters do HDInsight 3.5 + Linux Storm

Para saber como usar o Spout do hub de eventos Storm mais recente que funciona com clusters Storm do HDInsight 3.5 + Linux, consulte o [arquivo Leiame do MVN-repositório](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Exemplos de código-fonte

Veja [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) de como ler e gravar no Hub de eventos do Azure usando uma topologia de Apache Storm (escrita em Java) em um cluster HDInsight do Azure.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Como fazer localizar arquivos de configuração do Storm Log4J 2 em clusters?

Para identificar os arquivos de configuração do [Apache Log4J 2](https://logging.apache.org/log4j/2.x/) para serviços do Storm.

### <a name="on-head-nodes"></a>Em nós de cabeçalho

A configuração Nimbus Log4J é lida de `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>Em nós de trabalho

A configuração do Log4J do supervisor é lida no `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

O arquivo de configuração de Log4J de trabalho é lido no `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Exemplos: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Não é uma exceção de líder

Ao enviar uma topologia, o usuário pode receber uma mensagem de erro semelhante a: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

Para resolver, o usuário pode precisar arquivar um tíquete para que os nós sejam reiniciados/reinicializados. Para mais informações, consulte [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao suporte para Gestão de Subscrições e faturação está incluído na sua subscrição do Microsoft Azure e o Suporte Técnico é disponibilizado através de um dos [Planos de Suporte do Azure](https://azure.microsoft.com/support/plans/).
