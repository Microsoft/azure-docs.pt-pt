---
title: Ônibus de serviço Azure com .NET e AMQP 1.0 [ Microsoft Docs
description: Este artigo descreve como usar o Ônibus de Serviço Azure a partir de uma aplicação .NET usando AMQP (Protocolo de Fila de Mensagens Avançadas).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8157efac5ff1fc135659a84b4f4825ff36307480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297659"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Use ônibus de serviço de .NET com AMQP 1.0

O suporte AMQP 1.0 está disponível na versão 2.1 ou posterior do pacote de ônibus de serviço. Pode garantir que tem a versão mais recente baixando as bits do Service Bus do [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Configure as aplicações .NET para utilizar AMQP 1.0

Por padrão, a biblioteca de clientes Service Bus .NET comunica com o serviço service Bus utilizando um protocolo baseado em SABÃO. Para utilizar amqp 1.0 em vez do protocolo predefinido requer configuração explícita na cadeia de ligação do Ônibus de serviço, como descrito na secção seguinte. Para além desta alteração, o código de aplicação permanece inalterado ao utilizar amqp 1.0.

No lançamento atual, existem algumas funcionalidades DeAPI que não são suportadas na utilização de AMQP. Estas características não suportadas estão listadas na secção [Diferenças comportamentais.](#behavioral-differences) Algumas das configurações avançadas de configuração também têm um significado diferente ao usar AMQP.

### <a name="configuration-using-appconfig"></a>Configuração usando App.config

É uma boa prática para as aplicações utilizarem o ficheiro de configuração App.config para armazenar as definições. Para aplicações de ônibus de serviço, você pode usar App.config para armazenar a cadeia de ligação service bus. Um exemplo app.config ficheiro é o seguinte:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

O valor `Microsoft.ServiceBus.ConnectionString` da definição é a cadeia de ligação service bus que é usada para configurar a ligação ao Service Bus. O formato é o seguinte:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Onde `namespace` `SAS key` e são obtidos a partir do [portal Azure][Azure portal] quando você cria um espaço de nome de ônibus de serviço. Para mais informações, consulte Criar um espaço de [nome de ônibus de serviço utilizando o portal Azure][Create a Service Bus namespace using the Azure portal].

Ao utilizar amqp, acomode `;TransportType=Amqp`a cadeia de ligação com . Esta notação instrui a biblioteca do cliente a fazer a sua ligação ao Service Bus utilizando a AMQP 1.0.

## <a name="message-serialization"></a>Serialização de mensagens

Ao utilizar o protocolo predefinido, o comportamento de serialização padrão da biblioteca de clientes .NET é utilizar o tipo [DataContractSerializer][DataContractSerializer] para serializar uma instância [brokeredMessage][BrokeredMessage] para o transporte entre a biblioteca do cliente e o serviço service Bus. Ao utilizar o modo de transporte AMQP, a biblioteca do cliente utiliza o sistema do tipo AMQP para a serialização da [mensagem intermediada][BrokeredMessage] numa mensagem AMQP. Esta serialização permite que a mensagem seja recebida e interpretada por uma aplicação recetora que está potencialmente a funcionar numa plataforma diferente, por exemplo, uma aplicação Java que utiliza a API JMS para aceder ao Bus de Serviço.

Ao construir uma instância [BrokeredMessage,][BrokeredMessage] pode fornecer um objeto .NET como parâmetro ao construtor para servir como o corpo da mensagem. Para objetos que podem ser mapeados para tipos primitivos AMQP, o corpo é serializado em tipos de dados AMQP. Se o objeto não puder ser diretamente mapeado num tipo primitivo AMQP; isto é, um tipo personalizado definido pela aplicação, em seguida, o objeto é serializado usando o [DataContractSerializer][DataContractSerializer], e os bytes serializados são enviados numa mensagem de dados AMQP.

Para facilitar a interoperabilidade com non-.NET clientes, utilize apenas tipos .NET que podem ser serializados diretamente em tipos AMQP para o corpo da mensagem. A tabela seguinte detalha esses tipos e o mapeamento correspondente ao sistema do tipo AMQP.

| Tipo de objeto corporal .NET | Tipo AMQP mapeado | Tipo de secção corporal AMQP |
| --- | --- | --- |
| bool |boolean |Valor AMQP |
| byte |ubyte |Valor AMQP |
| ucurto |ucurto |Valor AMQP |
| uint |uint |Valor AMQP |
| ulongo |ulongo |Valor AMQP |
| sbyte |byte |Valor AMQP |
| curto |curto |Valor AMQP |
| int |int |Valor AMQP |
| longo |longo |Valor AMQP |
| float |float |Valor AMQP |
| double |double |Valor AMQP |
| decimal |decimal128 |Valor AMQP |
| char |char |Valor AMQP |
| DateTime |carimbo de data/hora |Valor AMQP |
| GUID |uuid |Valor AMQP |
| byte[] |binary |Valor AMQP |
| string |string |Valor AMQP |
| System.Collections.IList |list |Valor AMQP: os itens contidos na coleção só podem ser os definidos nesta tabela. |
| Sistema.Matriz |array |Valor AMQP: os itens contidos na coleção só podem ser os definidos nesta tabela. |
| System.Collections.IDictionary |map |Valor AMQP: os itens contidos na coleção só podem ser os definidos nesta tabela. Nota: apenas as teclas de corda são suportadas. |
| Uri |Cadeia descrita (ver a tabela seguinte) |Valor AMQP |
| DataTimeOffset |Descrito longo (ver a tabela seguinte) |Valor AMQP |
| TimeSpan |Descrito longo (ver o seguinte) |Valor AMQP |
| Transmitir em fluxo |binary |Dados AMQP (podem ser múltiplos). As secções de Dados contêm os bytes crus lidos a partir do objeto Stream. |
| Outro Objeto |binary |Dados AMQP (podem ser múltiplos). Contém o binário serializado do objeto que utiliza o DataContractSerializer ou um serializador fornecido pela aplicação. |

| Tipo .NET | Tipo descrito AMQP mapeado | Notas |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.Absoluteuri |
| DataTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Diferenças comportamentais

Existem algumas pequenas diferenças no comportamento do Bus de Serviço .NET API ao utilizar AMQP, em comparação com o protocolo padrão:

* A propriedade [OperationTimeout][OperationTimeout] é ignorada.
* `MessageReceiver.Receive(TimeSpan.Zero)`é implementado `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`como .
* O preenchimento de mensagens por fichas de bloqueio só pode ser feito pelos recetores de mensagens que receberam inicialmente as mensagens.

## <a name="control-amqp-protocol-settings"></a>Controlar as definições do protocolo AMQP

As [APIs .NET](/dotnet/api/) expõem várias configurações para controlar o comportamento do protocolo AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Controla o crédito inicial aplicado a um link. A predefinição é 0.
* **[MensagensFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Controla o tamanho máximo do quadro AMQP oferecido durante a negociação em tempo aberto de ligação. O padrão é de 65.536 bytes.
* **[MensagensFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Se as transferências forem lotáveis, este valor determina o atraso máximo para o envio de disposições. Herdado por remetentes/recetores por defeito. O remetente/recetor individual pode sobrepor-se à predefinição, que é de 20 milissegundos.
* **[MensagensFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Controla se as ligações AMQP são estabelecidas através de uma ligação TLS. O padrão é **verdade.**

## <a name="next-steps"></a>Passos seguintes

Está pronto para saber mais? Visite os seguintes links:

* [Visão geral do ônibus de serviço AMQP]
* [Guia do protocolo AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Visão geral do ônibus de serviço AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md

