---
title: 'Quickstart: Receba eventos usando Apache Storm - Azure Event Hubs'
description: 'Quickstart: Este artigo fornece informações sobre como receber eventos de Azure Event Hubs usando Apache Storm.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 099a3ff1ad57616de0d7929d787b82e9d55c9e00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87087582"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Quickstart: Receber eventos de Centros de Eventos usando a Tempestade Apache

[Apache Storm](https://storm.incubator.apache.org) é um sistema de computação distribuído em tempo real que simplifica o processamento fiável de fluxos de dados ilimitados. Esta secção mostra como usar um bico de tempestade Azure Event Hubs para receber eventos de Event Hubs. Usando a Tempestade Apache, você pode dividir eventos através de vários processos hospedados em diferentes nós. A integração do Event Hubs com a Storm simplifica o consumo de eventos, verificando de forma transparente o seu progresso utilizando a instalação do Zookeeper da Tempestade, gerindo pontos de verificação persistentes e receções paralelas dos Centros de Eventos.

Para obter mais informações sobre os Centros de Eventos, consulte a visão geral do [Event Hubs][Event Hubs overview].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o quickstart, **crie um espaço de nomes de Event Hubs e um centro de eventos.** Utilize o [portal Azure](https://portal.azure.com) para criar um espaço de nome de centros de eventos tipo, e obtenha as credenciais de gestão que a sua aplicação necessita para comunicar com o centro de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Criar projeto e adicionar código

Este tutorial utiliza uma instalação [HDInsight Storm,][HDInsight Storm] que vem com o bico de Evento Hubs já disponível.

1. Siga o [hdInsight Storm - Inicie](../hdinsight/storm/apache-storm-overview.md) o procedimento para criar um novo cluster HDInsight e conecte-o através do Ambiente de Trabalho Remoto.
2. Copie o `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` ficheiro para o seu ambiente de desenvolvimento local. Isto contém os eventos-storm-spout.
3. Utilize o seguinte comando para instalar a embalagem na loja local de Maven. Isto permite-lhe adicioná-lo como referência no projeto Storm num passo posterior.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. No Eclipse, crie um novo projeto Maven (clique em **File,** em seguida, **Novo,** em **seguida, Projeto).**
   
    ![Projeto De arquivo -> Novo->][12]
5. Selecione **Use a localização padrão do espaço de trabalho** e, em seguida, clique em **Seguinte**
6. Selecione o **arquétipo maven-arquétipo-quickstart** e, em seguida, clique em **Seguinte**
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

9. Na pasta **src,** crie um ficheiro chamado **Config.properties** e copie o seguinte conteúdo, substituindo os `receive rule key` valores e `event hub name` valores:

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
    O valor para **eventhub.receiver.credits** determina quantos eventos são lotados antes de os lançar no oleoduto Storm. Por uma questão de simplicidade, este exemplo define este valor para 10. Na produção, deve normalmente ser definida para valores mais elevados; por exemplo, 1024.
10. Criar uma nova classe chamada **LoggerBolt** com o seguinte código:
    
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
    
    Este parafuso de tempestade regista o conteúdo dos eventos recebidos. Isto pode ser facilmente estendido para armazenar tuples em um serviço de armazenamento. O [exemplo hdInsight Storm with Event Hub] utiliza esta mesma abordagem para armazenar dados em Azure Storage e Power BI.
11. Criar uma classe chamada **LogTopology** com o seguinte código:
    
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

    Esta classe cria um novo bico de Event Hubs, utilizando as propriedades do ficheiro de configuração para o instantanear. É importante notar que este exemplo cria tantas tarefas de bico como o número de divisórias no centro de eventos, de modo a utilizar o máximo de paralelismo permitido por esse centro de eventos.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs][Event Hubs overview]
* [Criar um centro de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Tempestade HDInsight com exemplo de Event Hub]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
