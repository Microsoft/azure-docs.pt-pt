---
title: Apache Kafka com Registo De Schema Confluente em Azure HDInsight
description: Implementar um cluster Kafka gerido por HDInsight com um Nó de Borda dentro de uma Rede Virtual e, em seguida, instale o Registo de Esquemas Confluentes no Nó de Borda.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500327"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka com Registo De Schema Confluente em Azure HDInsight

O Registo Kafka Schema fornece serializers que ligam os clientes Apache Kafka que lidam com o armazenamento de esquemas de mensagens e recuperação para mensagens Kafka que são enviadas no formato Avro. Costumava ser um projeto da OSS da Confluent, mas agora está sob a [licença comunitária confluente.](https://www.confluent.io/blog/license-changes-confluent-platform/) O Registo de Schema serve ainda os seguintes fins:

* Armazenar e recuperar esquemas para produtores e consumidores.
* Impor para trás/para a frente /compatibilidade total em Tópicos.
* Diminua o tamanho da carga enviada para Kafka.

Num cluster hdInsight Gerido kafka, o Registo de Schema é tipicamente implantado num nó de borda para permitir a separação computacional dos nós da cabeça.

Abaixo está uma arquitetura representativa de como o Registo de Schema é implantado em um cluster HDInsight. O Registo de Schema expõe de forma nativa uma API REST para operações nele.  Os produtores e consumidores podem interagir com o Registo de Schema a partir do VNet ou utilizando o [Kafka REST Proxy](rest-proxy.md).

![Registo hdInsight Kafka Schema](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Implementar um HDInsight gerido Kafka com registo de Schema Confluent

Nesta secção, implantamos um cluster HDInsight Managed Kafka com um Nó de Borda dentro de uma rede virtual e, em seguida, instalamos o Registo de Esquema Confluente no Nó de Borda.  

1. Selecione o botão **Implementar para Azure** abaixo para iniciar seduca no Azure e abra o modelo de Gestor de Recursos.

    [![Implementar no Azure](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. No modelo de implantação personalizado, povoe os campos conforme descrito abaixo:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|A partir da lista de drop-down, selecione a subscrição Azure que é usada para o cluster.|
    |Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente ou selecione **Criar novo**.|
    |Region|A partir da lista de drop-down, selecione uma região onde o cluster é criado.|
    |Nome do Cluster|Introduza um nome globalmente exclusivo. Ou deixar o as-is para usar o nome padrão.|
    |Nome de Utilizador de Início de Sessão do Cluster|Forneça o nome de utilizador, o padrão é **administrador**.|
    |Palavra-passe de Início de Sessão do Cluster|Forneça a senha.|
    |Nome do utilizador Ssh|Forneça o nome de utilizador, o padrão é **sshuser**.|
    |Ssh Password|Forneça a senha.|

    Deixe os outros campos como está. Selecione **Review + criar** para continuar.

1. Reveja os detalhes da implementação e, em seguida, **selecione Criar** para rubricar a implementação. A colocação pode demorar 45 minutos a ser concluída.

## <a name="configure-the-confluent-schema-registry"></a>Configure o Registo de Esquemas Confluentes

Nesta secção, configuramos o Registo De Schema De Kafka Confluente que instalamos no nó de borda. O registo de esquemas confluentes está localizado  `/etc/schema-registry/schema-registry.properties` e os mecanismos para iniciar e parar os executáveis de serviço estão localizados na  `/usr/bin/` pasta.

O Schema Register precisa de saber que o serviço Zookeeper pode interagir com o cluster HDInsight Kafka. Siga os passos abaixo para obter os detalhes do Zookeeper Quorum.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao nó de borda do cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Configurar a variável de senha. Substitua a PASSWORD pela palavra-passe de login do cluster e, em seguida, insira o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Extrair o nome do cluster corretamente arquivado. Execute o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Extrair os anfitriões kafka Zookeeper. Execute o seguinte comando:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Tome nota deste valor, uma vez que será usado mais tarde.

1. Extraia os anfitriões do corretor Kafka. Execute o seguinte comando:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Abra os ficheiros de propriedades do Registo de Schema no modo de edição. Execute o seguinte comando:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Atualize o valor para `kafkastore.connection.url` com a cadeia Zookeeper identificada anteriormente.
    1. Atualize o valor `debug` para `true` .

    O ficheiro de propriedades agora parece semelhante a:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Para guardar o ficheiro, utilize **Ctrl + X,** **Y,** e, em seguida, **Insira**.

1. Inicie o Registo de Schema e aponte-o para utilizar o ficheiro de propriedades do Registo de Schema atualizado. Execute os seguintes comandos:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Com o Registo de Schema em execução numa sessão de SSH, lance outra janela SSH.  Registar uma nova versão de um esquema sob o tema "Chave Kafka" e observar a saída:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Registar uma nova versão de um esquema sob o tema "Valor Kafka" e observar a saída:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Listar todos os sujeitos e verificar a saída:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

É melhor tentar outros [comandos avançados listados aqui.](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart)

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Enviar e consumir dados da Avro de Kafka usando o registo de esquemas

Nesta secção, vamos ler dados a partir da entrada padrão e escrevê-lo para um tópico Kafka em formato. Em seguida, vamos ler os dados dos Tópicos Kafka usando um consumidor com um formatter Avro para transformar esses dados em formato legível.

1. Crie um tópico `agkafkaschemareg` Kafka.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Use o **Produtor de Consola Kafka Avro** para criar um esquema, atribua o esquema ao tópico e comece a enviar dados para o tópico em formato Avro. Certifique-se de que o Tópico Kafka no passo anterior é criado com sucesso e que **$KAFKABROKERS** tem um valor nele.

    O esquema que estamos enviando é uma chave: Par de Valor

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Utilize o comando abaixo para iniciar o **Produtor de Consola Kafka Avro:**

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Quando o produtor estiver pronto para aceitar mensagens, comece a enviar as mensagens no formato de esquema avro predefinido. Utilize a tecla Guia para criar espaçamento entre a chave e o valor.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Tente introduzir dados aleatórios não-esquema no produtor de consolas para ver como o produtor permite agora quaisquer dados que não estejam em conformidade com o esquema avro predefinido.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. Num ecrã diferente, inicie o Kafka Avro Console Consumer

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Deve começar a ver a saída abaixo:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Passos seguintes

* [Utilize as APIs do Produtor Apache Kafka e do Consumidor](apache-kafka-producer-consumer-api.md)