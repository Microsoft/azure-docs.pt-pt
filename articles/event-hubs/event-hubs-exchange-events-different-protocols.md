---
title: Eventos entre aplicações que utilizam diferentes protocolos - Hubs de eventos do Exchange | Documentos da Microsoft
description: Este artigo mostra como os consumidores e produtores que utilizam diferentes protocolos (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao utilizar os Hubs de eventos do Azure.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: e704a2595130a2a815388447ac482ab96789d64a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453988"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Eventos de Exchange entre os consumidores e produtores que utilizam os protocolos diferentes: AMQP, Kafka e HTTPS
Os Hubs de eventos do Azure oferece suporte a três protocolos para consumidores e produtores: AMQP, Kafka e HTTPS. Cada um desses protocolos tem sua própria maneira de representar uma mensagem, naturalmente a seguinte questão é: se uma aplicação para enviar eventos para um Hub de eventos com um protocolo e consome-los com um protocolo diferente, o que fazer as várias partes e valores do evento aspeto quando chegam ao consumidor? Este artigo discute as práticas recomendadas para o produtor e consumidor garantir que os valores dentro de um evento corretamente são interpretados pela aplicação de consumo.

O Conselho neste artigo aborda especificamente estes clientes, com as versões listadas usadas no desenvolvimento os trechos de código:

* Cliente de Java do Kafka (versão 1.1.1 do https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure eventos dos Hubs de cliente para Java (a versão 1.1.0 do https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure eventos dos Hubs de cliente para o .NET (a versão 2.1.0 do https://github.com/Azure/azure-event-hubs-dotnet)
* (A versão 5.0.0 partir do Microsoft Azure Service Bus https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (suporta apenas os produtores)

Outros clientes AMQP podem ter um comportamento ligeiramente diferente. O AMQP tem um sistema de tipos bem definidas, mas as especificações dos tipos de idioma específico serializar de e para esse sistema de tipo depende do cliente, como faz, como o cliente fornece acesso a partes de uma mensagem AMQP.

## <a name="event-body"></a>Corpo de evento
Todos os clientes Microsoft AMQP representam o corpo de evento como uma matriz de bytes de não interpretado. Um aplicativo de produção passa uma seqüência de bytes para o cliente e uma aplicação de consumo recebe essa mesma sequência do cliente. A interpretação de seqüência de bytes acontece dentro do código de aplicação.

Ao enviar um evento através de HTTPS, o corpo de evento é o conteúdo de lançado, que também é tratado como bytes não interpretados. É fácil alcançar o mesmo Estado num consumidor ou produtor do Kafka com o fornecido ByteArraySerializer e ByteArrayDeserializer conforme mostrado no seguinte código:

### <a name="kafka-byte-producer"></a>Kafka byte[] producer

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] consumer
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Este código cria um pipeline de byte transparente entre as duas metades do aplicativo e permite ao desenvolvedor para manualmente serializar e desserializar de qualquer forma desejada, incluindo a tomada de decisões de desserialização em runtime, por exemplo com base no tipo ou informações do remetente nas propriedades do conjunto de usuários no evento.

Aplicativos que têm um único, o tipo de corpo de evento fixo pode ser capaz de usar outros Serializadores do Kafka e desserializadores converter forma transparente os dados. Por exemplo, considere um aplicativo, que usa JSON. A construção e a interpretação da cadeia de caracteres JSON ocorre ao nível do aplicativo. No nível de Hubs de eventos, o corpo de evento é sempre uma cadeia de caracteres, uma seqüência de bytes que representa os carateres da codificação UTF-8. Neste caso, o consumidor ou produtor do Kafka pode aproveitar o fornecido StringSerializer ou StringDeserializer conforme mostrado no seguinte código:

### <a name="kafka-utf-8-string-producer"></a>Produtor de cadeia de caracteres de Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Consumidor de cadeia de caracteres de Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Para o lado AMQP, Java e .NET fornecem formas incorporadas para converter cadeias de caracteres para e seqüências de bytes UTF-8. Os clientes Microsoft AMQP representam eventos como uma classe chamada EventData. Os exemplos seguintes mostram-lhe como serializar uma cadeia de caracteres UTF-8 num corpo de evento EventData num produtor AMQP e como anular a serialização de um corpo de evento EventData numa cadeia de caracteres UTF-8 num consumidor de AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Produtor de cadeia de caracteres de Java AMQP UTF-8
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Consumidor de cadeia de caracteres de Java AMQP UTF-8
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C#Produtor de cadeia de caracteres UTF-8 de .NET
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C#Consumidor de cadeia de caracteres UTF-8 de .NET
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Como o Kafka é aberto, o desenvolvedor de aplicativos pode inspecionar a implementação de qualquer serializador ou deserializer e implementar código, que produz ou consome uma seqüência compatível de bytes no lado do AMQP.

## <a name="event-user-properties"></a>Propriedades de utilizador do evento

Propriedades do conjunto de usuários podem ser definidas e obtidas a partir de ambos os clientes AMQP (em que os clientes Microsoft AMQP são denominados propriedades) e o Kafka (onde são chamadas de cabeçalhos). Os remetentes HTTPS podem definir propriedades de utilizador num evento, fornecendo-os como cabeçalhos HTTP na operação POST. No entanto, o Kafka trata os corpos de eventos e os valores de cabeçalho de eventos como seqüências de bytes. Ao passo que os clientes AMQP, valores de propriedade tem tipos, que são comunicados ao codificar os valores de propriedade, de acordo com o sistema de tipos AMQP.

O HTTPS é um caso especial. No ponto de enviar, todos os valores de propriedade são texto UTF-8. O serviço de Hubs de eventos faz uma quantidade limitada de interpretação para converter valores de propriedade apropriados com codificação AMQP 32 bits e 64 bits assinados números inteiros, números de ponto flutuante de 64 bits e booleanos. Qualquer valor de propriedade, que não se ajusta um desses tipos é tratado como uma cadeia de caracteres.

Combinar estas abordagens para a propriedade digitando significa que um consumidor de Kafka, verá a seqüência de bytes não processados AMQP codificados, incluindo as informações de tipo AMQP. Ao passo que um consumidor de AMQP verá a sequência de sem tipo de bytes enviada pelo produtor Kafka, o que o aplicativo deve interpretar.

Para os consumidores de Kafka que recebem as propriedades de produtores AMQP ou HTTPS, use a classe de AmqpDeserializer, que é modelada após os outros desserializadores no ecossistema do Kafka. Ele interpreta as informações de tipo de seqüências de bytes codificados de AMQP desserializar os bytes de dados num tipo de Java.

Como melhor prática, recomendamos que incluem uma propriedade em mensagens enviadas através de AMQP ou HTTPS. O consumidor de Kafka pode utilizá-lo para determinar se os valores de cabeçalho necessita de desserialização de AMQP. O valor da propriedade não é importante. Ele apenas precisa de um nome bem conhecido que o consumidor de Kafka pode encontrar na lista de cabeçalhos e ajuste o seu comportamento em conformidade.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP para parte de Kafka 1: criar e enviar um evento C# (.NET) com as propriedades
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP para parte de Kafka 2: utilizar AmqpDeserializer para anular a serialização essas propriedades num consumidor de Kafka
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

Se a aplicação sabe o tipo esperado para uma propriedade, existem métodos de anulação da serialização que não requerem um lançamento posteriormente, mas eles emite um erro se a propriedade não é do tipo esperado.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP para parte de Kafka 3: uma forma diferente do uso de AmqpDeserializer num consumidor de Kafka
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

Vai outra direção é mais envolvida, porque os cabeçalhos definidos por um produtor Kafka sempre são vistos por um consumidor AMQP como bytes não processados (escreva org.apache.qpid.proton.amqp.Binary para o cliente de Hubs de eventos do Microsoft Azure para Java, ou `System.Byte[]` da Microsoft .NET Clientes AMQP). O caminho mais fácil é usar um dos Serializadores fornecido pelo Kafka para gerar os bytes para os valores de cabeçalho no lado de produtor Kafka e, em seguida, escrever um código de desserialização compatível no lado do consumidor AMQP.

Tal como acontece com o AMQP para Kafka, a melhor prática, recomendamos que é incluir uma propriedade em mensagens enviadas por meio do Kafka. O consumidor AMQP pode usar a propriedade para determinar se os valores de cabeçalho necessita de desserialização. O valor da propriedade não é importante. Ele apenas precisa de um nome bem conhecido que o consumidor AMQP pode encontrar na lista de cabeçalhos e ajuste o seu comportamento em conformidade. Se não é possível alterar o produtor Kafka, também é possível que a aplicação de consumo verificar se o valor da propriedade é de um tipo binário ou de byte e tentar com base no tipo de desserialização.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>O Kafka para parte AMQP 1: criar e enviar um evento do Kafka com propriedades
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>O Kafka para parte AMQP 2: manualmente anular a serialização essas propriedades na C# (.NET)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>O Kafka para parte AMQP 3: manualmente anular a serialização essas propriedades em Java
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

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a transmitir para os Hubs de Eventos ativados para Kafka, sem alterar os clientes de protocolo nem executar os seus próprios clusters. Para saber mais sobre os Hubs de eventos e os Hubs de eventos para o Kafka, veja os artigos seguintes:  

* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos sobre os Hubs de Eventos do GitHub do Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Uso [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [eventos de fluxo do Kafka no local para o Kafka ativado os Hubs de eventos na cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Aprenda a transmitir para o Kafka através de Hubs de eventos ativados [aplicativos nativos do Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), ou [Akka fluxos](event-hubs-kafka-akka-streams-tutorial.md)
