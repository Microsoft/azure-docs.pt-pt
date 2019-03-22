---
title: 'Tutorial: Utilizar o Apache Storm para ler e escrever dados com o Apache Kafka - Azure HDInsight'
description: Saiba como criar um pipeline de transmissão em fluxo com o Apache Storm e o Apache Kafka no HDInsight. Neste tutorial, vai utilizar os componentes KafkaBolt e KafkaSpout para transmitir em fluxo dados a partir do Kafka.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/06/2018
ms.openlocfilehash: dca789a850e5df58024d13b8f592765e55c39485
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316954"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Tutorial: Utilizar o Apache Storm com o Apache Kafka no HDInsight

Este tutorial demonstra como utilizar um [Apache Storm](https://storm.apache.org/) topologia para ler e escrever dados com [Apache Kafka](https://kafka.apache.org/) no HDInsight. Este tutorial também demonstra como manter os dados para o [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) armazenamento compatível no cluster do Storm.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Storm e Kafka
> * Compreender o código
> * Criar clusters do Kafka e do Storm
> * Criar a topologia
> * Configurar a topologia
> * Criar o tópico do Kafka
> * Iniciar as topologias
> * Parar as topologias
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com a criação de tópicos do Kafka. Para obter mais informações, veja o documento [Início rápido do Kafka no HDInsight](./kafka/apache-kafka-get-started.md).

* Familiaridade com a criação e implementação de soluções do Storm (topologias). Especificamente, as topologias que utilizam [Apache Storm fluxo](https://storm.apache.org/releases/current/flux.html) framework. Para obter mais informações, consulte a [criar uma topologia do Apache Storm no Java](./storm/apache-storm-develop-java-topology.md) documento.

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) ou superior. O HDInsight 3.5 ou superior requer o Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Um cliente SSH (precisa dos comandos `ssh` e `scp`). Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

As seguintes variáveis de ambiente podem ser definidas quando instalar o Java e o JDK na sua estação de trabalho de desenvolvimento. No entanto, deve certificar-se de que existem e que contêm os valores corretos para o seu sistema.

* `JAVA_HOME` - deve apontar para o diretório onde o JDK está instalado.
* `PATH` - deve conter os seguintes caminhos:
  
    * `JAVA_HOME` (ou o caminho equivalente).
    * `JAVA_HOME\bin` (ou o caminho equivalente).
    * O diretório onde o Maven está instalado.

> [!IMPORTANT]  
> Os passos neste documento requerem um grupo de recursos do Azure que contém um cluster do Storm no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Storm comunique diretamente com o cluster do Kafka.
> 
> Para sua comodidade, este documento tem uma ligação para o modelo que pode criar todos os recursos do Azure necessários. 
>
> Para obter mais informações sobre como utilizar o HDInsight numa rede virtual, veja o documento [Extend HDInsight using a virtual network](hdinsight-extend-hadoop-virtual-network.md) (Utilizar uma rede virtual para expandir o HDInsight).

## <a name="storm-and-kafka"></a>Storm e Kafka

O Apache Storm fornece vários componentes para trabalhar com o Apache Kafka. São utilizados os seguintes componentes neste tutorial:

* `org.apache.storm.kafka.KafkaSpout`: Este componente lê os dados do Kafka. Este componente baseia-se nos componentes seguintes:

    * `org.apache.storm.kafka.SpoutConfig`: Fornece uma configuração para o componente de spout.

    * `org.apache.storm.spout.SchemeAsMultiScheme` e `org.apache.storm.kafka.StringScheme`: Como os dados do Kafka são transformados numa cadeia de identificação do Storm.

* `org.apache.storm.kafka.bolt.KafkaBolt`: Este componente escreve dados para o Kafka. Este componente baseia-se nos componentes seguintes:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: Descreve o tópico que é escrito.

    * `org.apache.kafka.common.serialization.StringSerializer`: Configura o bolt para serializar dados como um valor de cadeia de caracteres.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: Mapas de estrutura de dados de cadeia de identificação usadas dentro da topologia do Storm para campos armazenados no Kafka.

Estes componentes estão disponíveis no pacote `org.apache.storm : storm-kafka`. Utilize a versão do pacote que corresponde à versão do Storm. Para o HDInsight 3.6, a versão do Storm é a 1.1.0.
Também precisa do pacote `org.apache.kafka : kafka_2.10`, que contém os componentes adicionais do Kafka. Utilize a versão do pacote que corresponde à versão do Kafka. Para o HDInsight 3.6, a versão do Kafka é a 0.10.0.0.

O XML a seguir é a declaração de dependência na `pom.xml` para uma [Apache Maven](https://maven.apache.org/) projeto:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>0.10.0.0</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Compreender o código

O código utilizado neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Este tutorial proporciona duas topologias:

* Escritor de Kafka: Gera frases aleatórias e armazena-os ao Kafka.

* Kafka-reader: Lê os dados do Kafka e, em seguida, armazena-o para o arquivo de ficheiro compatível com HDFS para o cluster do Storm.

    > [!WARNING]  
    > Para que o Storm funcione com o armazenamento compatível com o HDFS que o HDInsight utiliza, é necessária uma ação de script. O script instala vários ficheiros jar no caminho `extlib` para o Storm. O modelo neste tutorial utiliza automaticamente o script durante a criação do cluster.
    >
    > Se não utilizar o modelo disponibilizado neste documento para criar o cluster do Storm, tem de aplicar manualmente a ação de script no cluster.
    >
    > A ação de script está localizada em [ https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh ](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) e é aplicada a nós do Storm cluster supervisor e nimbus. Para obter mais informações sobre como utilizar as ações de script, veja o documento [Customize HDInsight using script actions](hdinsight-hadoop-customize-cluster-linux.md) (Utilizar ações de script para personalizar o HDInsight).

As topologias são definidas com [Flux](https://storm.apache.org/releases/1.1.2/flux.html). O Flux foi introduzido no Storm 0.10.x e permite-lhe separar a configuração da topologia do código. As topologias que utilizam a estrutura Flux estão definidas num ficheiro YAML. O ficheiro YAML pode ser incluído como parte da topologia. Também pode ser um ficheiro autónomo utilizado quando submeter a topologia. O Flux também suporta a substituição de variáveis em tempo de execução, que é utilizada neste exemplo.

Os seguintes parâmetros são definidos em tempo de execução para estas topologias:

* `${kafka.topic}`: O nome do que as topologias de leitura/escritam para o tópico do Kafka.

* `${kafka.broker.hosts}`: Os anfitriões que o Kafka Media executam no. As informações dos mediadores são utilizadas pelo KafkaBolt quando escreve no Kafka.

* `${kafka.zookeeper.hosts}`: Os anfitriões Zookeeper é executado do cluster do Kafka.

* `${hdfs.url}`: O URL de sistema de ficheiros para o componente de HDFSBolt. Indica se os dados são escritos para uma conta de armazenamento do Azure ou o armazenamento do Azure Data Lake.

* `${hdfs.write.dir}`: O diretório de dados são gravados.

Para obter mais informações sobre as topologias Flux, veja [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

### <a name="kafka-writer"></a>Kafka-writer

Na topologia Kafka-writer, o componente de bolt do Kafka recebe dois valores de cadeia como parâmetros. Estes parâmetros indicam quais os campos de tuple que o bolt envia para o Kafka como valores de __chave__ e __mensagem__. A chave é utilizada para criar partições de dados no Kafka. A mensagem são os dados que estão a ser armazenados.

Neste exemplo, o componente `com.microsoft.example.SentenceSpout` emite um tuple que contém dois campos, `key` e `message`. O bolt do Kafka extrai esses campos e envia os dados contidos nos mesmos para o Kafka.

Os campos não têm de utilizar os nomes `key` e `message`. Estes nomes são utilizados neste projeto para facilitar a compreensão do mapeamento.

O seguinte YAML é a definição do componente Kafka-writer:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-reader

Na topologia Kafka-reader, o componente de spout lê os dados de Kafka como valores de cadeia. Depois, os dados são escritos no registo do Storm por parte do componente de registo e no sistema de ficheiros compatível com o HDFS do cluster do Storm por parte do componente de bolt do HDFS.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Substituições de propriedades

O projeto contém um ficheiro denominado `dev.properties` e que é utilizado para transmitir os parâmetros que as topologias utilizam. Define as seguintes propriedades:

| ficheiro dev.properties | Descrição |
| --- | --- |
| `kafka.zookeeper.hosts` | O [Apache ZooKeeper](https://zookeeper.apache.org/) anfitriões para o cluster do Kafka. |
| `kafka.broker.hosts` | Os anfitriões dos mediadores do Kafka (nós de trabalho). |
| `kafka.topic` | O tópico do Kafka que as topologias utilizam. |
| `hdfs.write.dir` | O diretório no qual a topologia de Kafka-reader escreve. |
| `hdfs.url` | O sistema de ficheiros que o cluster do Storm utiliza. Nas contas de armazenamento do Azure, utilize o valor `wasb:///`. Para o ger2 de armazenamento do Azure Data Lake, utilize um valor de `abfs:///`. Para o Azure Data Lake Storage Gen1, utilize um valor de `adl:///`. |

## <a name="create-the-clusters"></a>Criar os clusters

O Apache Kafka no HDInsight não fornece acesso aos mediadores Kafka através da Internet pública. Tudo o que utilize Kafka tem de estar na mesma rede virtual do Azure. Neste tutorial, os clusters do Kafka e do Storm estão localizados na mesma rede virtual do Azure. 

O diagrama seguinte mostra como a comunicação flui entre o Storm e o Kafka:

![Diagrama de clusters do Storm e do Kafka numa rede virtual do Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]  
> Outros serviços em cluster como SSH e [Apache Ambari](https://ambari.apache.org/) podem ser acedidos através da internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para criar uma Rede Virtual do Azure e, depois, criar os clusters do Kafka e do Storm na mesma, utilize os passos abaixo:

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo do Azure Resource Manager está localizado em **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. Cria os seguintes recursos:
    
    * Grupo de recursos do Azure
    * Rede Virtual do Azure
    * Conta de armazenamento do Azure
    * Kafka no HDInsight versão 3.6 (três nós de trabalho)
    * Storm no HDInsight versão 3.6 (três nós de trabalho)

   > [!WARNING]  
   > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster do Kafka que contém três nós de trabalho.

2. Utilize a seguinte orientação para preencher as entradas da secção **Implementação personalizada**:

   1. Utilize as seguintes informações para preencher as entradas da secção **Modelo personalizado**:

      | Definição | Valor |
      | --- | --- |
      | Subscrição | A sua subscrição do Azure |
      | Grupo de recursos | O grupo de recursos que contém os recursos. |
      | Localização | A região do Azure na qual os recursos são criados. |
      | Nome do Cluster do Kafka | O nome do cluster do Kafka. |
      | Nome do Cluster do Storm | O nome do cluster do Storm. |
      | Nome de Utilizador de Início de Sessão do Cluster | O nome de utilizador administrador para os clusters. |
      | Palavra-passe de Início de Sessão do Cluster | A palavra-passe de utilizador administrador para os clusters. |
      | Nome de Utilizador SSH | O nome de utilizador SSH para os clusters. |
      | Palavra-passe do SSH | A palavra-passe do utilizador SSH. |
   
      ![Imagem dos parâmetros de modelo](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

4. Por fim, marque **Afixar ao dashboard** e, em seguida, selecione **Compra**.

> [!NOTE]  
> A criação dos clusters pode demorar até 20 minutos.

## <a name="build-the-topology"></a>Criar a topologia

1. No seu ambiente de desenvolvimento, transfira o projeto a partir de [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), abra uma linha de comandos e altere os diretórios para a localização na qual transferiu o projeto.

2. No diretório **hdinsight-storm-java-kafka**, utilize o seguinte comando para compilar o projeto e criar um pacote para implementação:

   ```bash
   mvn clean package
   ```

    O processo do pacote cria um ficheiro denominado `KafkaTopology-1.0-SNAPSHOT.jar` no diretório `target`.

3. Utilize os seguintes comandos para copiar o pacote para o cluster do Storm no HDInsight. Substitua `sshuser` pelo nome de utilizador do SSH do cluster. Substitua `stormclustername` pelo nome do cluster do __Storm__.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    Quando lhe for pedido, introduza a palavra-passe que utilizou quando criou os clusters.

## <a name="configure-the-topology"></a>Configurar a topologia

1. Utilize um dos seguintes métodos para detetar os anfitriões dos mediadores do Kafka para o cluster do **Kafka** no HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > O seguinte exemplo de Bash pressupõe que `$CLUSTERNAME` contém o nome do cluster do __Kafka__. Também pressupõe que está instalada a versão 1.5 ou superior do [jq](https://stedolan.github.io/jq/). Quando lhe for pedido, introduza a palavra-passe da conta de início de sessão do cluster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    O valor devolvido é semelhante ao seguinte texto:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]  
    > Embora possa haver mais de dois anfitriões de mediadores para o cluster, não tem de fornecer uma lista completa de todos os anfitriões aos clientes. Basta um ou dois.

2. Utilize um dos seguintes métodos para detetar os anfitriões do Zookeeper para o cluster do __Kafka__ no HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > O seguinte exemplo de Bash pressupõe que `$CLUSTERNAME` contém o nome do cluster do __Kafka__. Também pressupõe que [jq](https://stedolan.github.io/jq/) está instalado. Quando lhe for pedido, introduza a palavra-passe da conta de início de sessão do cluster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    O valor devolvido é semelhante ao seguinte texto:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]  
    > Embora haja mais de dois nós do Zookeeper, não tem de fornecer uma lista completa de todos os anfitriões para os clientes. Basta um ou dois.

    Guarde este valor, porque vai ser utilizado mais tarde.

3. Edite o ficheiro `dev.properties` na raiz do projeto. Adicione as informações dos anfitriões dos mediadores e do Zookeeper para o cluster do __Kafka__ às linhas correspondentes neste ficheiro. O exemplo seguinte é configurado com os valores de exemplo dos passos anteriores:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]  
    > A entrada `hdfs.url` está configurada para um cluster que utiliza uma conta de armazenamento do Azure. Para utilizar esta topologia com um cluster do Storm que utiliza o armazenamento do Data Lake, altere este valor de `wasb` para `adl`.

4. Guarde o ficheiro `dev.properties` e, em seguida, utilize o seguinte comando para carregá-lo para o cluster do **Storm**:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Substitua **USERNAME** pelo nome de utilizador do SSH do cluster. Substitua **BASENAME** pelo nome de base que utilizou quando criou o cluster.

## <a name="create-the-kafka-topic"></a>Criar o tópico do Kafka

O Kafka armazena os dados num _tópico_. Tem de criar o tópico antes de iniciar as topologias do Storm. Para criar a topologia, utilize os seguintes passos:

1. Utilize o comando abaixo para ligar ao cluster do __Kafka__ através do SSH. Substitua `sshuser` pelo nome de utilizador do SSH que utilizou quando criou o cluster. Substitua `kafkaclustername` pelo nome do cluster do Kafka:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Quando lhe for pedido, introduza a palavra-passe que utilizou quando criou os clusters.
   
    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para criar o tópico do Kafka, utilize o seguinte comando. Substitua `$KAFKAZKHOSTS` pelas informações de anfitrião do Zookeeper que utilizou quando configurou a topologia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Este comando liga ao Zookeeper para o cluster do Kafka e cria um tópico novo com o nome `stormtopic`. Este tópico é utilizado pelas topologias do Storm.

## <a name="start-the-writer"></a>Iniciar o escritor

1. Utilize o seguinte para ligar ao cluster do **Storm** através do SSH. Substitua `sshuser` pelo nome de utilizador do SSH que utilizou quando criou o cluster. Substitua `stormclustername` pelo nome do cluster do Storm:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Quando lhe for pedido, introduza a palavra-passe que utilizou quando criou os clusters.
   
    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A partir da ligação SSH ao cluster do Storm, utilize o comando seguinte para iniciar a topologia de escritor:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Os parâmetros utilizados neste comando são:

    * `org.apache.storm.flux.Flux`: Utilize o fluxo para configurar e executar esta topologia.

    * `--remote`: Submeta a topologia para Nimbus. A topologia é distribuída em todos os nós de trabalho no cluster.

    * `-R /writer.yaml`: Utilize o `writer.yaml` ficheiro para configurar a topologia. `-R` indica que este recurso está incluído no ficheiro jar. Está na raiz do jar, pelo que `/writer.yaml` é o caminho para o mesmo.

    * `--filter`: Preencher as entradas na `writer.yaml` topologia usando valores no `dev.properties` ficheiro. Por exemplo, o valor da entrada `kafka.topic` no ficheiro é utilizado para substituir a entrada `${kafka.topic}` na definição da topologia.

## <a name="start-the-reader"></a>Iniciar o leitor

1. A partir da sessão do SSH para o cluster do Storm, utilize o comando seguinte para iniciar a topologia de leitor:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Aguarde um minuto e, em seguida, utilize o seguinte comando para ver os ficheiros criados pela topologia de leitor:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    O resultado é semelhante ao seguinte texto:

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. Para ver os conteúdos do ficheiro, utilize o seguinte comando. Substitua `filename.txt` pelo nome de um ficheiro:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    O texto seguinte é um exemplo dos conteúdos do ficheiro:

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Parar as topologias

A partir de uma sessão do SSH no cluster do Storm, utilize os comandos seguintes para parar as topologias do Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

> [!WARNING]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado.
> 
> Eliminar um cluster do Kafka no HDInsight elimina quaisquer dados armazenados no Kafka.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar um [Apache Storm](https://storm.apache.org/) topologia para gravar e ler a partir do [Apache Kafka](https://kafka.apache.org/) no HDInsight. Também aprendeu a armazenar os dados para o [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) compatível armazenamento utilizado pelo HDInsight.

Para saber mais sobre como utilizar o Kafka no HDInsight, consulte a [utilizar o Apache produtor Kafka e API de consumidor](kafka/apache-kafka-producer-consumer-api.md) documento.

Para obter informações sobre como implementar e monitorizar as topologias no HDInsight baseado em Linux, veja [Deploy and manage Apache Storm topologies on Linux-based HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) (Implementar e gerir topologias do Apache Storm no HDInsight baseado em Linux).
