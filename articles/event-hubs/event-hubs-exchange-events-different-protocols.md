---
title: Azure Event Hubs - Trocar eventos usando diferentes protocolos
description: Este artigo mostra como os consumidores e produtores que usam diferentes protocolos (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao utilizarem os Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010325"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Intercâmbio de eventos entre consumidores e produtores que utilizem diferentes protocolos: AMQP, Kafka e HTTPS
A Azure Event Hubs apoia três protocolos para consumidores e produtores: AMQP, Kafka e HTTPS. Cada um destes protocolos tem a sua própria forma de representar uma mensagem, pelo que, naturalmente, surge a seguinte questão: se uma aplicação envia eventos para um Centro de Eventos com um protocolo e os consome com um protocolo diferente, como são as várias partes e valores do evento quando chegam ao consumidor? Este artigo discute as melhores práticas tanto para o produtor como para o consumidor para garantir que os valores dentro de um evento sejam corretamente interpretados pela aplicação consumista.

O parecer deste artigo abrange especificamente estes clientes, com as versões listadas utilizadas no desenvolvimento dos snippets de código:

* Cliente Kafka Java (versão 1.1.1 de https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs Cliente para Java (versão 1.1.0 a partir de https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs Cliente para .NET (versão 2.1.0 a partir de https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (versão 5.0.0 de https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (só apoia produtores)

Outros clientes amqp podem comportar-se de forma ligeiramente diferente. A AMQP tem um sistema de tipo bem definido, mas as especificidades de serializar tipos específicos da linguagem de e para esse sistema de tipo dependem do cliente, assim como a forma como o cliente fornece acesso às partes de uma mensagem AMQP.

## <a name="event-body"></a>Corpo de Evento
Todos os clientes amQP da Microsoft representam o corpo do evento como um saco de bytes não interpretado. Uma aplicação de produção transmite uma sequência de bytes ao cliente, e uma aplicação consumista recebe essa mesma sequência do cliente. A interpretação da sequência byte ocorre dentro do código de aplicação.

Ao enviar um evento via HTTPS, o corpo do evento é o conteúdo POSTed, que também é tratado como bytes não interpretados. É fácil alcançar o mesmo estado num produtor ou consumidor kafka utilizando o byteArraySerializer e ByteArrayDeserializer como mostrado no seguinte código:

### <a name="kafka-byte-producer"></a>Produtor kafka byte[] produtor

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] consumidor
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Este código cria um gasoduto de byte transparente entre as duas metades da aplicação e permite ao desenvolvedor de aplicações serializar e deserizar manualmente de qualquer forma desejada, incluindo tomar decisões de dessericialização em tempo de execução, por exemplo com base em informações de tipo ou remetente em propriedades definidas pelo utilizador no evento.

As aplicações que tenham um único tipo de corpo de evento fixo podem ser capazes de usar outros serializers kafka, e deserializadores para converter de forma transparente dados. Por exemplo, considere uma aplicação, que usa JSON. A construção e interpretação da cadeia JSON acontece ao nível da aplicação. Ao nível dos Centros de Eventos, o corpo do evento é sempre uma corda, uma sequência de bytes que representam caracteres na codificação UTF-8. Neste caso, o produtor ou consumidor kafka pode tirar partido do stringSerializer ou StringDeserializer fornecido, conforme indicado no seguinte código:

### <a name="kafka-utf-8-string-producer"></a>Produtor de cordas Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 consumidor de cordas
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Para o lado AMQP, tanto Java como .NET fornecem formas incorporadas de converter cordas de e para as sequências de byte UTF-8. Os clientes AMQP da Microsoft representam eventos como uma classe chamada EventData. Os exemplos que se seguem mostram-lhe como serializar uma corda UTF-8 num corpo de eventos EventData num produtor amQP e como deserializar um corpo de eventos EventData numa cadeia UTF-8 num consumidor AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Produtor de cordas Java AMQP UTF-8
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 consumidor de cordas
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 produtor de cordas
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 consumidor de cordas
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Como a Kafka é de código aberto, o desenvolvedor de aplicações pode inspecionar a implementação de qualquer serializer ou desserializador e implementar código, que produz ou consome uma sequência compatível de bytes no lado AMQP.

## <a name="event-user-properties"></a>Propriedades do utilizador do evento

As propriedades definidas pelo utilizador podem ser definidas e recuperadas tanto dos clientes AMQP (nos clientes AMQP da Microsoft são chamadas de propriedades) como da Kafka (onde são chamados cabeçalhos). Os remetentes HTTPS podem definir as propriedades do utilizador num evento, fornecendo-as como cabeçalhos HTTP na operação POST. No entanto, Kafka trata os órgãos de evento e os valores do cabeçalho do evento como sequências byte. Enquanto que nos clientes AMQP, os valores imobiliários têm tipos, que são comunicados codificando os valores de propriedade de acordo com o sistema de tipo AMQP.

HTTPS é um caso especial. No ponto de envio, todos os valores de propriedade são texto UTF-8. O serviço Event Hubs faz uma quantidade limitada de interpretação para converter valores de propriedade apropriados para números de 32 bits e 64 bits assinados por AMQP, números de ponto flutuante de 64 bits e booleans. Qualquer valor patrimonial, que não se enquadre num desses tipos, é tratado como uma corda.

Misturar estas abordagens à dactilografia de propriedades significa que um consumidor de Kafka vê a sequência de byte cru codificada por AMQP, incluindo a informação do tipo AMQP. Considerando que um consumidor de AMQP vê a sequência de byte não escrita enviada pelo produtor Kafka, que o pedido deve interpretar.

Para os consumidores de Kafka que recebem propriedades de produtores AMQP ou HTTPS, utilize a classe AmqpDeserializer, que é modelada após os outros deserializadores no ecossistema kafka. Interpreta a informação tipo nas sequências de byte codificadas pela AMQP para deserializar os bytes de dados num tipo de Java.

Como uma boa prática, recomendamos que inclua um imóvel em mensagens enviadas via AMQP ou HTTPS. O consumidor de Kafka pode usá-lo para determinar se os valores do cabeçalho precisam de deserialização amqp. O valor do imóvel não é importante. Só precisa de um nome bem conhecido que o consumidor de Kafka possa encontrar na lista de cabeçalhos e ajustar o seu comportamento em conformidade.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP para a parte 1 de Kafka: criar e enviar um evento em C# (.NET) com propriedades
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP para Kafka parte 2: use AmqpDeserializer para dessaializar essas propriedades num consumidor kafka
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Se a aplicação sabe o tipo esperado para um imóvel, existem métodos de deserialização que não requerem um elenco posteriormente, mas eles lançam um erro se a propriedade não for do tipo esperado.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP para Kafka parte 3: uma forma diferente de usar amqpDeserializador em um consumidor kafka
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Ir na outra direção é mais envolvido, porque os cabeçalhos definidos por um produtor kafka são sempre vistos por um consumidor amQP como bytes crus (type org.apache.qpid.proton.amqp.Binary para o Microsoft Azure Event Hubs Client for Java, ou `System.Byte[]` para os clientes Microsoft .NET AMQP). O caminho mais fácil é utilizar um dos serializers fornecidos pela Kafka para gerar os bytes para os valores do cabeçalho do lado do produtor kafka e, em seguida, escrever um código de dessasseialização compatível no lado do consumidor AMQP.

Tal como acontece com a AMQP-to-Kafka, a melhor prática que recomendamos é incluir uma propriedade em mensagens enviadas via Kafka. O consumidor amQP pode usar a propriedade para determinar se os valores do cabeçalho precisam de deserialização. O valor do imóvel não é importante. Só precisa de um nome bem conhecido que o consumidor de AMQP possa encontrar na lista de cabeçalhos e ajustar o seu comportamento em conformidade. Se o produtor kafka não puder ser alterado, também é possível que o pedido de consumo verifique se o valor da propriedade é de um tipo binário ou byte e tentar a dessesserização com base no tipo.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka para AMQP parte 1: criar e enviar um evento de Kafka com propriedades
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka para AMQP parte 2: deserializar manualmente essas propriedades em C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka para AMQP parte 3: deserializar manualmente essas propriedades em Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a transmitir para os Centros de Eventos sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. Para saber mais sobre Os Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

* [Saiba mais sobre Hubs de Eventos](./event-hubs-about.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos sobre os Hubs de Eventos do GitHub do Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Use [o MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos de Kafka nas instalações para Os Centros de Eventos na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir em Centros de Eventos usando [aplicações nativas de Kafka,](event-hubs-quickstart-kafka-enabled-event-hubs.md) [Apache Flink](event-hubs-kafka-flink-tutorial.md)ou [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
