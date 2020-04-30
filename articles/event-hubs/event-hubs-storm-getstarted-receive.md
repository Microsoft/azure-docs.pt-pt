---
title: 'Quickstart: Receba eventos usando Apache Storm - Azure Event Hubs'
description: 'Quickstart: Este artigo fornece informações sobre como receber eventos de Azure Event Hubs usando a Tempestade Apache.'
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
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 90293da07d3a7ef1c32e5f82d35198d4ffa536b1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73717608"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Quickstart: Receba eventos de Centros de Eventos usando a Tempestade Apache

[Apache Storm](https://storm.incubator.apache.org) é um sistema de computação em tempo real distribuído que simplifica o processamento fiável de fluxos de dados não limitados. Esta secção mostra como usar um bico de tempestade Azure Event Hubs Storm para receber eventos de Event Hubs. Usando a Tempestade Apache, você pode dividir eventos em vários processos hospedados em diferentes nós. A integração dos Hubs de Eventos com a Tempestade simplifica o consumo de eventos, verificando transparentemente o seu progresso utilizando a instalação do Zookeeper da Tempestade, gerindo pontos de verificação persistentes e recebeções paralelas dos Centros de Eventos.

Para mais informações sobre os Hubs de Eventos, receba padrões, consulte a visão geral do [Event Hubs][Event Hubs overview].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o quickstart, **crie um espaço de nome do Event Hubs e um hub de eventos.** Utilize o [portal Azure](https://portal.azure.com) para criar um espaço de nome de Hubs de Eventos tipo, e obtenha as credenciais de gestão que a sua aplicação precisa para comunicar com o hub do evento. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo.](event-hubs-create.md) 

## <a name="create-project-and-add-code"></a>Criar projeto e adicionar código

Este tutorial utiliza uma instalação [de tempestade HDInsight,][HDInsight Storm] que vem com o bico de Hubs de Eventojá disponível.

1. Siga o procedimento [HDInsight Storm - Get Started](../hdinsight/storm/apache-storm-overview.md) para criar um novo cluster HDInsight e ligue-o através do Ambiente de Trabalho Remoto.
2. Copie `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` o ficheiro para o seu ambiente de desenvolvimento local. Isto contém o spout de tempestade de eventos.
3. Utilize o seguinte comando para instalar a embalagem na loja Local Maven. Isto permite adicioná-lo como uma referência no projeto Storm em um passo posterior.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. No Eclipse, crie um novo projeto Maven (clique em **File**, em seguida **Novo,** em **seguida, Projeto).**
   
    ![Arquivo -> Novo Projeto de >][12]
5. Selecione Utilizar a localização do espaço de **trabalho predefinido**e, em seguida, clique **em Next**
6. Selecione o arquétipo **maven-arquétipo-quickstart** e, em seguida, clique **em Seguinte**
7. Insira um **GroupId** e **ArtifactId,** em seguida, clique em **Terminar**
8. Em **pom.xml,** adicione as seguintes dependências no `<dependency>` nó.

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

9. Na pasta **SRC,** crie um ficheiro chamado **Config.properties** e `receive rule key` `event hub name` copie o seguinte conteúdo, substituindo os valores e valores:

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
    O valor para **eventhub.receiver.credits** determina quantos eventos são loteados antes de os lançar para o oleoduto Storm. Por uma questão de simplicidade, este exemplo define este valor para 10. Na produção, deve normalmente ser fixado a valores mais elevados; por exemplo, 1024.
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
    
    Este parafuso storm regista o conteúdo dos eventos recebidos. Isto pode ser facilmente estendido para armazenar tuples num serviço de armazenamento. A [tempestade HDInsight com o exemplo do Event Hub] utiliza esta mesma abordagem para armazenar dados em Azure Storage e Power BI.
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

    Esta classe cria um novo bico de Hubs de Eventos, utilizando as propriedades no ficheiro de configuração para o instantaneamente. É importante notar que este exemplo cria tantas tarefas de bicocomo o número de divisórias no centro do evento, de forma a utilizar o paralelismo máximo permitido por esse centro de eventos.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Tempestade HDInsight com exemplo de Hub de Evento]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
