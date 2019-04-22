---
title: Receber eventos com o Apache Storm - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como receber eventos dos Event Hubs do Azure com o Apache Storm.
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
ms.openlocfilehash: 75a96127c48186befc48b2240f78e49cd5914239
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679266"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Receber eventos dos Hubs de eventos com o Apache Storm

[O Apache Storm](https://storm.incubator.apache.org) é um sistema em tempo real de computação distribuída que simplifica o processamento fiável de fluxos independentes de dados. Esta secção mostra como utilizar um spout do Storm de Hubs de eventos do Azure para receber eventos dos Hubs de eventos. Utilizar o Apache Storm, pode dividir eventos por vários processos alojados em nós diferentes. A integração de Hubs de eventos com o Storm simplifica do consumo de eventos de forma transparente pontos de verificação seu progresso usando a instalação de Zookeeper do Storm, gerenciamento de pontos de verificação persistentes e receções em paralelo Hubs de eventos.

Para obter mais informações sobre os Hubs de eventos receber padrões, consulte a [descrição geral dos Hubs de eventos][Event Hubs overview].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o guia de introdução **criar um espaço de nomes de Hubs de eventos e um hub de eventos**. Utilize o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de eventos e obter as credenciais de gestão a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [este artigo](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Crie o projeto e adicione o código

Este tutorial utiliza uma [Storm do HDInsight] [ HDInsight Storm] instalação, o que é fornecido com o spout de Hubs de eventos já disponível.

1. Siga os [Storm do HDInsight - introdução ao](../hdinsight/storm/apache-storm-overview.md) procedimento para criar um novo cluster do HDInsight e ligá-la através do ambiente de trabalho remoto.
2. Copiar o `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` ficheiro ao seu ambiente de desenvolvimento local. Contém o spout de storm de eventos.
3. Utilize o seguinte comando para instalar o pacote para o arquivo local do Maven. Isto permite-lhe adicioná-la como uma referência no projeto Storm num passo posterior.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. No Eclipse, crie um novo projeto Maven (clique em **arquivo**, em seguida, **New**, em seguida, **projeto**).
   
    ![Ficheiro -> novo -> projeto][12]
5. Selecione **utilizar a localização de área de trabalho predefinida**, em seguida, clique em **seguinte**
6. Selecione o **maven-mvn-quickstart** mvn, em seguida, clique em **seguinte**
7. Inserir um **GroupId** e **ArtifactId**, em seguida, clique em **concluir**
8. Na **pom. XML**, adicione as seguintes dependências no `<dependency>` nó.

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

9. Na **src** pasta, crie um ficheiro chamado **Config. Properties** e copie o seguinte conteúdo, substituindo o `receive rule key` e `event hub name` valores:

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
    O valor para **eventhub.receiver.credits** determina o número de eventos é limitativas antes do lançamento-los para o pipeline de Storm. Para simplificar, este exemplo define esse valor para o 10. Na produção, deve normalmente ser definida para valores mais altos; Por exemplo, 1024.
10. Criar uma nova classe chamada **LoggerBolt** com o código a seguir:
    
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
    
    Este bolt do Storm regista o conteúdo dos eventos recebidos. Isso pode facilmente ser estendido para armazenar cadeias de identificação de um serviço de armazenamento. O [Storm do HDInsight com o exemplo do Hub de eventos] utiliza essa mesma abordagem para armazenar dados no armazenamento do Azure e o Power BI.
11. Criar uma classe chamada **LogTopology** com o código a seguir:
    
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

    Essa classe cria um novo spout de Hubs de eventos, usando as propriedades no ficheiro de configuração para criar uma instância dele. É importante ter em atenção que este exemplo cria todas as tarefas spouts como o número de partições num hub de eventos, para poder utilizar o paralelismo máximo permitido por esse hub de eventos.

## <a name="next-steps"></a>Passos Seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Storm do HDInsight com o exemplo do Hub de eventos]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
