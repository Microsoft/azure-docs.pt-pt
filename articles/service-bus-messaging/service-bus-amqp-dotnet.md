---
title: Autocarro de serviço Azure com .NET e AMQP 1.0 | Microsoft Docs
description: Este artigo descreve como utilizar o Azure Service Bus a partir de uma aplicação .NET utilizando AMQP (Protocolo avançado de fila de mensagens).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 20800363327aefda073cd484dc737b28e60466a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632855"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Utilize o ônibus de serviço de .NET com AMQP 1.0

O suporte AMQP 1.0 está disponível na versão 2.1 ou posterior do pacote Service Bus. Pode garantir que tem a versão mais recente, baixando as bits do Service Bus da [NuGet.][NuGet]

> [!NOTE]
> Pode utilizar o Protocolo de Fila de Mensagens Avançadas (AMQP) ou o Protocolo de Mensagens de Autocarros de Serviço (SBMP) com a biblioteca .NET para Service Bus. AMQP é o protocolo predefinido utilizado pela biblioteca .NET. Recomendamos que utilize o protocolo AMQP (que é o padrão) e não o substitua. 

## <a name="configure-net-applications-to-use-amqp-10"></a>Configure .NET aplicações para utilizar AMQP 1.0

Por predefinição, a biblioteca de clientes Service Bus .NET comunica com o serviço service bus utilizando o protocolo AMQP. Também pode especificar explicitamente amQP como o tipo de transporte como mostrado na secção seguinte. 

Na versão atual, existem algumas funcionalidades da API que não são suportadas na utilização de AMQP. Estas funcionalidades não suportadas estão listadas na secção [Diferenças comportamentais.](#behavioral-differences) Algumas das configurações avançadas também têm um significado diferente quando se utiliza AMQP.

### <a name="configuration-using-appconfig"></a>Configuração usando App.config

É uma boa prática para as aplicações utilizarem o ficheiro de configuração App.config para armazenar as definições. Para aplicações Service Bus, pode utilizar App.config para armazenar a cadeia de ligação Service Bus. Um exemplo App.config ficheiro é o seguinte:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

O valor da definição é a cadeia de `Microsoft.ServiceBus.ConnectionString` ligação Service Bus que é utilizada para configurar a ligação ao Service Bus. O formato é o seguinte:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Onde `namespace` e `SAS key` são obtidos a partir do portal [Azure][Azure portal] quando cria um espaço de nomes de Service Bus. Para obter mais informações, consulte [Criar um espaço de nomes de ônibus de serviço utilizando o portal Azure.][Create a Service Bus namespace using the Azure portal]

Ao utilizar amQP, apecende a cadeia de ligação com `;TransportType=Amqp` . Esta notação instrui a biblioteca do cliente a fazer a sua ligação ao Service Bus utilizando AMQP 1.0.

### <a name="amqp-over-websockets"></a>AMQP através de WebSockets
Para utilizar AMQP sobre WebSockets, coloque `TransportType` na cadeia de ligação para `AmqpWebSockets` . Por exemplo: `Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`. 

Se estiver a utilizar a biblioteca .NET Microsoft.Azure.ServiceBus, defina o [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) para AmqpWebSockets of [TransportType enum](/dotnet/api/microsoft.azure.servicebus.transporttype).

Se estiver a utilizar a biblioteca .NET Azure.Messaging.ServiceBus, defina o [ServiceBusClient.TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclient.transporttype) para AmqpWebSockets of [ServiceBusTransportType enum](/dotnet/api/azure.messaging.servicebus.servicebustransporttype).


## <a name="message-serialization"></a>Serialização de mensagens

Ao utilizar o protocolo predefinido, o comportamento de serialização padrão da biblioteca de clientes .NET é utilizar o tipo [DataContractSerializer][DataContractSerializer] para serializar uma instância [Demessage Brokered][BrokeredMessage] para transporte entre a biblioteca do cliente e o serviço Service Bus. Ao utilizar o modo de transporte AMQP, a biblioteca do cliente utiliza o sistema de tipo AMQP para serialização da [mensagem intermediada][BrokeredMessage] numa mensagem AMQP. Esta serialização permite que a mensagem seja recebida e interpretada por uma aplicação recetora que está potencialmente a funcionar numa plataforma diferente, por exemplo, uma aplicação Java que utiliza a API JMS para aceder ao Service Bus.

Quando constrói uma instância [de Compensação de Intermediários,][BrokeredMessage] pode fornecer um objeto .NET como parâmetro para o construtor servir como o corpo da mensagem. Para objetos que podem ser mapeados para tipos primitivos AMQP, o corpo é serializado em tipos de dados AMQP. Se o objeto não puder ser diretamente mapeado num tipo primitivo AMQP; ou seja, um tipo personalizado definido pela aplicação, então o objeto é serializado usando o [DataContractSerializer][DataContractSerializer], e os bytes serializados são enviados numa mensagem de dados AMQP.

Para facilitar a interoperabilidade com non-.NET clientes, utilize apenas tipos .NET que possam ser serializados diretamente nos tipos amQP para o corpo da mensagem. O quadro seguinte detalha esses tipos e o mapeamento correspondente ao sistema de tipo AMQP.

| .NET Body Object Type | Tipo AMQP mapeado | Tipo de secção de corpo AMQP |
| --- | --- | --- |
| bool |boolean |Valor AMQP |
| byte |ubyte |Valor AMQP |
| ushort |ushort |Valor AMQP |
| uint |uint |Valor AMQP |
| ulong |ulong |Valor AMQP |
| sbyte |byte |Valor AMQP |
| curto |curto |Valor AMQP |
| int |int |Valor AMQP |
| long |long |Valor AMQP |
| float |float |Valor AMQP |
| double |double |Valor AMQP |
| decimal |decimal128 |Valor AMQP |
| char |char |Valor AMQP |
| DateTime |carimbo de data/hora |Valor AMQP |
| GUID |uuid |Valor AMQP |
| byte[] |binary |Valor AMQP |
| string |string |Valor AMQP |
| System.Collections.IList |lista |Valor AMQP: os itens contidos na coleção só podem ser os que estão definidos nesta tabela. |
| Sistema.Array |matriz |Valor AMQP: os itens contidos na coleção só podem ser os que estão definidos nesta tabela. |
| System.Collections.IDictionary |mapa |Valor AMQP: os itens contidos na coleção só podem ser os que estão definidos nesta tabela. Nota: apenas as teclas de corda são suportadas. |
| Uri |Cadeia descrita (ver tabela a seguir) |Valor AMQP |
| Início de execução de tempo de data |Descrito longo (ver tabela a seguir) |Valor AMQP |
| TimeSpan |Descrito longo (ver o seguinte) |Valor AMQP |
| Fluxo |binary |Dados amqp (pode ser múltiplo). As secções de Dados contêm os bytes crus lidos a partir do objeto Stream. |
| Outro objeto |binary |Dados amqp (pode ser múltiplo). Contém o binário serializado do objeto que utiliza o DataContractSerializer ou um serializer fornecido pela aplicação. |

| .NET Tipo | Tipo descrito amqp mapeado | Notas |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| Início de execução de tempo de data |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DataTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Diferenças comportamentais

Existem algumas pequenas diferenças no comportamento do Service Bus .NET API ao utilizar amQP, em comparação com o protocolo padrão:

* A propriedade [OperationTimeout][OperationTimeout] é ignorada.
* `MessageReceiver.Receive(TimeSpan.Zero)` é implementado como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` .
* A conclusão das mensagens por fichas de bloqueio só pode ser feita pelos recetores de mensagens que inicialmente receberam as mensagens.

## <a name="control-amqp-protocol-settings"></a>Definições do protocolo AMQP de controlo

As [APIs .NET](/dotnet/api/) expõem várias configurações para controlar o comportamento do protocolo AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Controla o crédito inicial aplicado a um link. A predefinição é 0.
* **[MensagensFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Controla o tamanho máximo do quadro AMQP oferecido durante a negociação em tempo aberto de ligação. O padrão é de 65.536 bytes.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Se as transferências forem lotáveis, este valor determina o atraso máximo para o envio de disposições. Herdado por remetentes/recetores por defeito. O remetente/recetor individual pode anular o padrão, que é de 20 milissegundos.
* **[MensagensFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Controla se as ligações AMQP são estabelecidas sobre uma ligação TLS. O padrão é **verdadeiro.**

## <a name="next-steps"></a>Passos seguintes

Está pronto para saber mais? Visite os seguintes links:

* [Visão geral do AMQP do autocarro de serviço]
* [Guia do protocolo AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Visão geral do AMQP do autocarro de serviço]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md