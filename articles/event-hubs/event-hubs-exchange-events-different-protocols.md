---
title: Azure Event Hubs - Eventos de intercâmbio usando diferentes protocolos
description: Este artigo mostra como os consumidores e produtores que utilizam diferentes protocolos (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao utilizar em Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 368cc568c40e878338e6b45205e74cba1d0b6378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372205"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Troca de eventos entre consumidores e produtores que utilizam diferentes protocolos: AMQP, Kafka e HTTPS
O Azure Event Hubs apoia três protocolos para consumidores e produtores: AMQP, Kafka e HTTPS. Cada um destes protocolos tem a sua própria forma de representar uma mensagem, por isso, naturalmente, surge a seguinte questão: se uma aplicação envia eventos para um Hub de Eventos com um protocolo e os consome com um protocolo diferente, o que fazem as várias partes e valores do evento parece quando chegam ao consumidor? Este artigo discute as melhores práticas tanto para o produtor como para o consumidor, a fim de garantir que os valores dentro de um evento sejam corretamente interpretados pela aplicação consumista.

Os conselhos deste artigo abrangem especificamente estes clientes, com as versões listadas utilizadas no desenvolvimento dos códigos:

* Cliente Kafka Java (versão 1.1.1 dehttps://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs Cliente para Java (versão 1.1.0https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs Client para .NET (versão 2.1.0 dehttps://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (versão 5.0.0https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (apoia apenas os produtores)

Outros clientes da AMQP podem comportar-se de forma ligeiramente diferente. A AMQP tem um sistema de tipo bem definido, mas as especificidades de serializar tipos específicos de linguagem de e para esse sistema de tipo dependem do cliente, assim como a forma como o cliente fornece acesso às partes de uma mensagem AMQP.

## <a name="event-body"></a>Corpo de Evento
Todos os clientes da Microsoft AMQP representam o corpo do evento como um saco não interpretado de bytes. Uma aplicação produtora passa uma sequência de bytes ao cliente, e uma aplicação consumista recebe essa mesma sequência do cliente. A interpretação da sequência byte ocorre dentro do código de aplicação.

Ao enviar um evento via HTTPS, o organismo do evento é o conteúdo POSTed, que também é tratado como bytes não interpretados. É fácil obter o mesmo estado num produtor ou consumidor de Kafka utilizando o ByteArraySerializer e byteArrayDeserializer fornecidos, como mostrado no seguinte código:

### <a name="kafka-byte-producer"></a>Kafka byte[] produtor

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

Este código cria um pipeline de byte transparente entre as duas metades da aplicação e permite ao desenvolvedor de aplicações serializar e desserializar manualmente de qualquer forma desejada, incluindo tomar decisões de desserialização no prazo de funcionamento, por exemplo com base no tipo ou informações de remetente em propriedades definidas pelo utilizador no evento.

Aplicações que tenham um único tipo de corpo de evento fixo podem ser capazes de usar outros serializadores kafka, e desserializers para converter dados transparentemente. Por exemplo, considere uma aplicação, que utiliza a JSON. A construção e interpretação da cadeia JSON acontece ao nível da aplicação. Ao nível dos Centros de Eventos, o corpo do evento é sempre uma sequência de bytes que representam personagens na codificação UTF-8. Neste caso, o produtor ou consumidor kafka pode tirar partido do StringSerializer ou StringDeserializer fornecidos, como mostrado no seguinte código:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 produtor de cordas
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

Para o lado AMQP, tanto java como .NET fornecem formas incorporadas de converter cordas de e para sequências de byteUTF-8. Os clientes da Microsoft AMQP representam eventos como uma classe chamada EventData. Os exemplos seguintes mostram como serializar uma cadeia UTF-8 num organismo de eventos EventData num produtor amqp, e como desserializar um corpo de eventos eventData numa cadeia UTF-8 num consumidor AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 produtor de cordas
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

Como kafka é de código aberto, o desenvolvedor de aplicações pode inspecionar a implementação de qualquer serializador ou desserializer e implementar código, que produz ou consome uma sequência compatível de bytes no lado amqp.

## <a name="event-user-properties"></a>Propriedades do utilizador do evento

As propriedades de conjunto de utilizadores podem ser definidas e recuperadas tanto dos clientes AMQP (nos clientes DA Microsoft AMQP são chamadas de propriedades) e kafka (onde são chamados cabeçalhos). Os remetentes HTTPS podem definir as propriedades dos utilizadores num evento, fornecendo-as como cabeçalhos HTTP na operação POST. No entanto, Kafka trata os corpos do evento e os valores do cabeçalho do evento como sequências byte. Enquanto nos clientes AMQP, os valores patrimoniais têm tipos, que são comunicados codificando os valores de propriedade de acordo com o sistema do tipo AMQP.

HTTPS é um caso especial. No ponto de envio, todos os valores de propriedade são texto UTF-8. O serviço Event Hubs faz uma quantidade limitada de interpretação para converter valores de propriedade apropriados para inteiros assinados de 32 bits e 64 bits codificados por AMQP, números de pontos flutuantes de 64 bits e booleans. Qualquer valor de propriedade, que não se encaixa num desses tipos é tratado como uma corda.

Misturar estas abordagens à dactilografia de propriedade significa que um consumidor de Kafka vê a sequência de byte codificada por AMQP, incluindo a informação do tipo AMQP. Considerando que um consumidor da AMQP vê a sequência de byte não digitada enviada pelo produtor de Kafka, que o pedido deve interpretar.

Para os consumidores de Kafka que recebem propriedades de produtores AMQP ou HTTPS, utilize a classe AmqpDeserializer, que é modelada após os outros desserializers no ecossistema de Kafka. Interpreta a informação do tipo nas sequências de byte codificadas por AMQP para desserializar os bytes de dados num tipo Java.

Como uma boa prática, recomendamos que inclua uma propriedade em mensagens enviadas via AMQP ou HTTPS. O consumidor de Kafka pode usá-lo para determinar se os valores do cabeçalho precisam de desserialização amqp. O valor do imóvel não é importante. Só precisa de um nome bem conhecido que o consumidor de Kafka possa encontrar na lista de cabeçalhos e ajustar o seu comportamento em conformidade.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP para kafka parte 1: criar e enviar um evento em C# (.NET) com propriedades
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP para kafka parte 2: use AmqpDeserializer para desserializar essas propriedades num consumidor de Kafka
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

Se a aplicação conhece o tipo esperado para um imóvel, existem métodos de desserialização que não requerem um elenco posteriormente, mas atiram um erro se a propriedade não for do tipo esperado.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP para Kafka parte 3: uma forma diferente de usar AmqpDeserializer num consumidor de Kafka
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

Ir na outra direção é mais envolvido, porque os cabeçalhos definidos por um produtor de Kafka são sempre vistos por um consumidor de AMQP como bytes crus (tipo org.apache.qpid.proton.amqp.Binary para o Microsoft Azure Event Hubs Client for Java, ou `System.Byte[]` para os clientes .NET AMQP da Microsoft). O caminho mais fácil é usar um dos serializadores fornecidos por Kafka para gerar os bytes para os valores do cabeçalho do lado do produtor kafka, e, em seguida, escrever um código de desserialização compatível no lado do consumidor AMQP.

Tal como acontece com a AMQP-to-Kafka, a melhor prática que recomendamos é incluir uma propriedade em mensagens enviadas via Kafka. O consumidor AMQP pode usar a propriedade para determinar se os valores do cabeçalho precisam de ser desserializados. O valor do imóvel não é importante. Só precisa de um nome bem conhecido que o consumidor da AMQP possa encontrar na lista de cabeçalhos e ajustar o seu comportamento em conformidade. Se o produtor de Kafka não puder ser alterado, também é possível que a aplicação consumista verifique se o valor da propriedade é de um tipo binário ou byte e tentar desserialização com base no tipo.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka para a amqp parte 1: criar e enviar um evento de Kafka com propriedades
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka para amqp parte 2: desserializar manualmente essas propriedades em C# (.NET)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka para amqp parte 3: desserializar manualmente essas propriedades em Java
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
Neste artigo, aprendeu a transmitir para O Hubs de Eventos sem alterar os seus clientes protocolares ou executar os seus próprios clusters. Para saber mais sobre Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos sobre os Hubs de Eventos do GitHub do Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Use [mirrormaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos de Kafka nas instalações para Event Hubs na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir para Centros de Eventos usando [aplicações nativas](event-hubs-quickstart-kafka-enabled-event-hubs.md)de Kafka, [Apache Flink](event-hubs-kafka-flink-tutorial.md)ou [Streams Akka](event-hubs-kafka-akka-streams-tutorial.md)
