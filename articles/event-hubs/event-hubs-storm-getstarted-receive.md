---
title: Receber eventos usando o Apache Storm-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como receber eventos dos hubs de eventos do Azure usando Apache Storm.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: eaa461dd0c4ef6bd9ed0ae4379a710ee100929d2
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800192"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Receber eventos dos hubs de eventos usando Apache Storm

O [Apache Storm](https://storm.incubator.apache.org) é um sistema de computação em tempo real distribuído que simplifica o processamento confiável de fluxos de dados não associados. Esta seção mostra como usar um Spout do Storm dos hubs de eventos do Azure para receber eventos de hubs de eventos. Usando Apache Storm, você pode dividir eventos em vários processos hospedados em nós diferentes. A integração dos hubs de eventos com o Storm simplifica o consumo de eventos, fazendo um ponto de verificação transparente de seu progresso usando a instalação do Zookeeper do Storm, gerenciando pontos de verificação persistentes e recebimentos paralelos dos hubs de eventos.

Para obter mais informações sobre padrões de recebimento de hubs de eventos, consulte a [visão geral dos hubs de eventos][Event Hubs overview].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o início rápido, **crie um namespace de hubs de eventos e um hub de eventos**. Use o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo hubs de eventos e obter as credenciais de gerenciamento que seu aplicativo precisa para se comunicar com o Hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento neste [artigo](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Criar projeto e adicionar código

Este tutorial usa uma instalação do [HDInsight Storm][HDInsight Storm] , que vem com o Spout dos hubs de eventos já disponível.

1. Siga o procedimento [HDInsight Storm-](../hdinsight/storm/apache-storm-overview.md) introdução para criar um novo cluster hdinsight e conectar-se a ele por meio de área de trabalho remota.
2. Copie o arquivo `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` em seu ambiente de desenvolvimento local. Ele contém os eventos-Storm-Spout.
3. Use o comando a seguir para instalar o pacote no repositório Maven local. Isso permite que você o adicione como uma referência no projeto Storm em uma etapa posterior.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. No Eclipse, crie um novo projeto Maven (clique em **arquivo**, **novo**e **projeto**).
   
    ![Arquivo-> Novo > projeto][12]
5. Selecione **usar local do espaço de trabalho padrão**e clique em **Avançar**
6. Selecione o arquétipo **Maven-arquétipoe-QuickStart** e clique em **Avançar**
7. Insira um **GroupId** e um **artefatoid**e clique em **concluir**
8. No **pom. xml**, adicione as seguintes dependências no nó `<dependency>`.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. Na pasta **src** , crie um arquivo chamado **config. Properties** e copie o conteúdo a seguir, substituindo os valores de `receive rule key` e `event hub name`:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    O valor de **eventhub. receiver. créditos** determina quantos eventos são incluídos em lote antes de liberá-los para o pipeline Storm. Para simplificar, este exemplo define esse valor como 10. Em produção, normalmente deve ser definido como valores mais altos; por exemplo, 1024.
10. Crie uma nova classe chamada **LoggerBolt** com o seguinte código:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Esse parafuso do Storm registra o conteúdo dos eventos recebidos. Isso pode ser facilmente estendido para armazenar tuplas em um serviço de armazenamento. O [HDInsight Storm com exemplo de Hub de eventos] usa essa mesma abordagem para armazenar dados no armazenamento do Azure e Power bi.
11. Crie uma classe chamada **LogTopology** com o seguinte código:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Essa classe cria um novo Spout de hubs de eventos, usando as propriedades no arquivo de configuração para instanciá-lo. É importante observar que este exemplo cria tantas tarefas de esgotamento quanto o número de partições no Hub de eventos, a fim de usar o paralelismo máximo permitido pelo hub de eventos.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm com exemplo de Hub de eventos]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
