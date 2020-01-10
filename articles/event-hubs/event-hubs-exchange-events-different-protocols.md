---
title: Hubs de eventos do Azure – trocar eventos usando protocolos diferentes
description: Este artigo mostra como os consumidores e produtores que usam protocolos diferentes (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao usar os hubs de eventos do Azure.
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
ms.date: 12/20/2019
ms.author: bahariri
ms.openlocfilehash: aecde0c36fc48f75e5174ca3e1ab9e2b3476d08a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437190"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Trocar eventos entre consumidores e produtores que usam protocolos diferentes: AMQP, Kafka e HTTPS
Os hubs de eventos do Azure dão suporte a três protocolos para consumidores e produtores: AMQP, Kafka e HTTPS. Cada um desses protocolos tem sua própria maneira de representar uma mensagem, portanto, naturalmente, a seguinte pergunta ocorre: se um aplicativo envia eventos para um hub de eventos com um protocolo e os consome com um protocolo diferente, o que faz as várias partes e valores do o evento parece quando chega ao consumidor? Este artigo aborda as práticas recomendadas para o produtor e o consumidor para garantir que os valores dentro de um evento sejam interpretados corretamente pelo aplicativo de consumo.

O Conselho neste artigo aborda especificamente esses clientes, com as versões listadas usadas no desenvolvimento dos trechos de código:

* Cliente Java Kafka (versão 1.1.1 de https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure cliente dos hubs de eventos para Java (versão 1.1.0 da https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure o cliente dos hubs de eventos para .NET (versão 2.1.0 do https://github.com/Azure/azure-event-hubs-dotnet)
* Barramento de Serviço do Microsoft Azure (versão 5.0.0 do https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (dá suporte apenas a produtores)

Outros clientes do AMQP podem se comportar de forma ligeiramente diferente. O AMQP tem um sistema de tipos bem definido, mas as especificidades de serialização de tipos específicos de idiomas de e para esse sistema de tipos dependem do cliente, assim como o cliente fornece acesso às partes de uma mensagem AMQP.

## <a name="event-body"></a>Corpo do evento
Todos os clientes do Microsoft AMQP representam o corpo do evento como um recipiente não interpretado de bytes. Um aplicativo de produção passa uma sequência de bytes para o cliente e um aplicativo de consumo recebe essa mesma sequência do cliente. A interpretação da sequência de bytes ocorre dentro do código do aplicativo.

Ao enviar um evento via HTTPS, o corpo do evento é o conteúdo postado, que também é tratado como bytes não interpretados. É fácil alcançar o mesmo estado em um produtor ou consumidor de Kafka usando o ByteArraySerializer e ByteArrayDeserializer fornecidos, conforme mostrado no código a seguir:

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

### <a name="kafka-byte-consumer"></a>Consumidor do Kafka byte []
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Esse código cria um pipeline de byte transparente entre as duas metades do aplicativo e permite que o desenvolvedor do aplicativo Serialize e Desserialize manualmente de qualquer forma desejada, incluindo tomar decisões de desserialização em tempo de execução, por exemplo, com base no tipo ou informações do remetente em propriedades definidas pelo usuário no evento.

Os aplicativos que têm um tipo de corpo de evento único fixo podem ser capazes de usar outros serializadores Kafka e desserializadores para converter dados de forma transparente. Por exemplo, considere um aplicativo, que usa JSON. A construção e a interpretação da cadeia de caracteres JSON acontecem no nível do aplicativo. No nível dos hubs de eventos, o corpo do evento é sempre uma cadeia de caracteres, uma sequência de bytes que representa caracteres na codificação UTF-8. Nesse caso, o produtor ou o consumidor do Kafka pode aproveitar o StringSerializer ou o StringDeserializer fornecido, conforme mostrado no código a seguir:

### <a name="kafka-utf-8-string-producer"></a>Produtor de cadeia de caracteres UTF-8 Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Consumidor de cadeia de caracteres UTF-8 Kafka
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Para o lado do AMQP, o Java e o .NET fornecem maneiras internas de converter cadeias de caracteres para e de sequências de bytes UTF-8. Os clientes do Microsoft AMQP representam eventos como uma classe denominada EventData. Os exemplos a seguir mostram como serializar uma cadeia de caracteres UTF-8 em um corpo de evento EventData em um produtor de AMQP e como desserializar um corpo de evento EventData em uma cadeia de caracteres UTF-8 em um consumidor AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Produtor de cadeia de caracteres UTF-8 Java AMQP
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Consumidor de cadeia de caracteres UTF-8 Java AMQP
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C#Produtor de cadeia de caracteres UTF-8 .NET
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C#Consumidor de cadeia de caracteres .NET UTF-8
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Como o Kafka é de código-fonte aberto, o desenvolvedor do aplicativo pode inspecionar a implementação de qualquer serializador ou desserializador e implementar o código, que produz ou consome uma sequência compatível de bytes no lado do AMQP.

## <a name="event-user-properties"></a>Propriedades do usuário de evento

As propriedades definidas pelo usuário podem ser definidas e recuperadas de ambos os clientes AMQP (nos clientes do Microsoft AMQP, eles são chamados de propriedades) e Kafka (onde são chamados de cabeçalhos). Remetentes HTTPS podem definir propriedades de usuário em um evento fornecendo-os como cabeçalhos HTTP na operação POST. No entanto, Kafka trata os corpos de eventos e os valores de cabeçalho de evento como sequências de bytes. Enquanto em clientes AMQP, os valores de propriedade têm tipos, que são comunicados codificando os valores de propriedade de acordo com o sistema de tipo AMQP.

HTTPS é um caso especial. No ponto de envio, todos os valores de propriedade são texto UTF-8. O serviço de hubs de eventos faz uma quantidade limitada de interpretação para converter os valores de propriedade apropriados em inteiros de AMQP de 32 bits e de 64 bits com sinal, números de ponto flutuante de 64 bits e boolianos. Qualquer valor de propriedade, que não caiba em um desses tipos, é tratado como uma cadeia de caracteres.

Misturar essas abordagens à digitação de propriedade significa que um consumidor Kafka vê a sequência de bytes codificada AMQP bruta, incluindo as informações de tipo AMQP. Enquanto um consumidor AMQP vê a sequência de bytes não tipada enviada pelo produtor do Kafka, que o aplicativo deve interpretar.

Para consumidores Kafka que recebem Propriedades de produtores AMQP ou HTTPS, use a classe AmqpDeserializer, que é modelada após os outros desserializadores no ecossistema Kafka. Ele interpreta as informações de tipo nas sequências de bytes codificados em AMQP para desserializar os bytes de dados em um tipo Java.

Como prática recomendada, recomendamos que você inclua uma propriedade em mensagens enviadas por meio de AMQP ou HTTPS. O consumidor Kafka pode usá-lo para determinar se os valores de cabeçalho precisam de desserialização de AMQP. O valor da propriedade não é importante. Ele só precisa de um nome conhecido que o consumidor Kafka possa encontrar na lista de cabeçalhos e ajustar seu comportamento de acordo.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP Kafka parte 1: criar e enviar um evento no C# (.net) com propriedades
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP Kafka parte 2: usar AmqpDeserializer para desserializar essas propriedades em um consumidor de Kafka
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

Se o aplicativo souber o tipo esperado para uma propriedade, haverá métodos de desserialização que não exigem uma conversão posteriormente, mas eles lançarão um erro se a propriedade não for do tipo esperado.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP Kafka parte 3: uma maneira diferente de usar o AmqpDeserializer em um consumidor de Kafka
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

A outra direção é mais envolvida, porque os cabeçalhos definidos por um produtor de Kafka são sempre vistos por um consumidor AMQP como bytes brutos (tipo org. Apache. QPID. Proton. AMQP. Binary para o Microsoft Azure cliente dos hubs de eventos para Java ou `System.Byte[]` para clientes .NET AMQP da Microsoft). O caminho mais fácil é usar um dos serializadores fornecidos pelo Kafka para gerar os bytes para os valores de cabeçalho no lado do produtor do Kafka e, em seguida, escrever um código de desserialização compatível no lado do consumidor AMQP.

Assim como com AMQP-to-Kafka, a melhor prática que recomendamos é incluir uma propriedade em mensagens enviadas por meio de Kafka. O consumidor AMQP pode usar a propriedade para determinar se os valores de cabeçalho precisam de desserialização. O valor da propriedade não é importante. Ele só precisa de um nome conhecido que o consumidor AMQP possa encontrar na lista de cabeçalhos e ajustar seu comportamento de acordo. Se o produtor do Kafka não puder ser alterado, também é possível que o aplicativo de consumo Verifique se o valor da propriedade é de um tipo binário ou de byte e tente desserialização com base no tipo.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka AMQP parte 1: criar e enviar um evento de Kafka com propriedades
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka AMQP parte 2: desserializar manualmente essas propriedades no C# (.net)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka AMQP parte 3: desserializar manualmente essas propriedades em Java
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
Neste artigo, aprendeu a transmitir para os Hubs de Eventos ativados para Kafka, sem alterar os clientes de protocolo nem executar os seus próprios clusters. Para saber mais sobre hubs de eventos e hubs de eventos para Kafka, consulte os seguintes artigos:  

* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos sobre os Hubs de Eventos do GitHub do Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Use [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos do Kafka local para os hubs de eventos do Kafka habilitados na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir em hubs de eventos habilitados para Kafka usando [aplicativos Kafka nativos](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md)ou [fluxos de Akka](event-hubs-kafka-akka-streams-tutorial.md)
