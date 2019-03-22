---
title: Barramento de serviço do Azure com .NET e AMQP 1.0 | Documentos da Microsoft
description: Utilizar o Azure Service Bus do .NET com AMQP
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 96d84b2328ffb83d5c1ea7218b17314a537fa45b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904563"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Utilizar o Service Bus do .NET com o AMQP 1.0

Suporte de AMQP 1.0 está disponível na versão de pacote do Service Bus 2.1 ou posterior. Pode certificar-se tiver a versão mais recente ao transferir os bits de barramento de serviço do [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Configurar aplicações de .NET para utilizar o AMQP 1.0

Por predefinição, a biblioteca de cliente .NET do Service Bus se comunica com o serviço do Service Bus com um protocolo baseado em SOAP dedicado. Para utilizar o AMQP 1.0 em vez da predefinição protocolo requer configuração explícita na cadeia de ligação do Service Bus, conforme descrito na secção seguinte. Além desta alteração, o código do aplicativo permanece inalterado ao utilizar o AMQP 1.0.

Na versão atual, existem algumas funcionalidades de API que não são suportadas ao utilizar o AMQP. Estas funcionalidades não suportadas estão listadas na secção [diferenças de comportamento](#behavioral-differences). Algumas das definições de configuração avançada também têm um significado diferente ao utilizar o AMQP.

### <a name="configuration-using-appconfig"></a>Configuração com o App. config

É uma boa prática para aplicações para utilizar o ficheiro de configuração App. config para armazenar definições. Para aplicações do Service Bus, pode utilizar o App. config para armazenar a cadeia de ligação do Service Bus. Segue-se um exemplo de arquivo App. config:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

O valor da `Microsoft.ServiceBus.ConnectionString` definição é a cadeia de ligação do Service Bus que é utilizada para configurar a ligação ao Service Bus. O formato é o seguinte:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Em que `namespace` e `SAS key` são obtidos a partir do [portal do Azure] [ Azure portal] quando cria um espaço de nomes do Service Bus. Para obter mais informações, consulte [criar um espaço de nomes do Service Bus com o portal do Azure][Create a Service Bus namespace using the Azure portal].

Ao utilizar o AMQP, anexe a cadeia de ligação com `;TransportType=Amqp`. Este notação instrui a biblioteca de cliente para estabelecer a ligação ao Service Bus através de AMQP 1.0.

## <a name="message-serialization"></a>Serialização de mensagem

Ao utilizar o protocolo de padrão, o comportamento de serialização padrão da biblioteca de cliente .NET é usar o [DataContractSerializer] [ DataContractSerializer] tipo para serializar um [BrokeredMessage] [ BrokeredMessage] instância para o transporte entre a biblioteca de cliente e o serviço do Service Bus. Ao usar o modo de transporte AMQP, a biblioteca de cliente usa o sistema de tipos AMQP para a serialização do [mensagem mediada] [ BrokeredMessage] numa mensagem de AMQP. Esta serialização permite que a mensagem ser recebida e é interpretado por um aplicativo de recebimento potencialmente em execução numa plataforma diferente, por exemplo, uma aplicação de Java que utiliza a API de JMS para aceder ao Service Bus.

Quando constrói um [BrokeredMessage] [ BrokeredMessage] instância, pode fornecer um objeto .NET como um parâmetro para o construtor para servir como corpo da mensagem. Para os objetos que podem ser mapeados para tipos primitivos de AMQP, o corpo é serializado em tipos de dados AMQP. Se o objeto não pode ser mapeado diretamente para um tipo primitivo de AMQP; ou seja, um tipo personalizado definido pelo aplicativo, então o objeto é serializado com o [DataContractSerializer][DataContractSerializer], e os bytes serializados são enviados numa mensagem de dados AMQP.

Para facilitar a interoperabilidade com clientes de ambientes não .NET, utilize apenas os tipos de .NET que podem ser serializados diretamente em tipos AMQP para o corpo da mensagem. A tabela seguinte fornece detalhes sobre esses tipos e o mapeamento correspondente para o sistema de tipos AMQP.

| Tipo de objeto do corpo de .NET | Tipo AMQP mapeada | Tipo de seção de corpo AMQP |
| --- | --- | --- |
| booleano |boolean |Valor AMQP |
| byte |ubyte |Valor AMQP |
| ushort |ushort |Valor AMQP |
| uint |uint |Valor AMQP |
| ulong |ulong |Valor AMQP |
| sbyte |byte |Valor AMQP |
| curto |curto |Valor AMQP |
| int |int |Valor AMQP |
| longa |longa |Valor AMQP |
| float |float |Valor AMQP |
| double |double |Valor AMQP |
| decimal |decimal128 |Valor AMQP |
| char |char |Valor AMQP |
| DateTime |carimbo de data/hora |Valor AMQP |
| Guid |uuid |Valor AMQP |
| byte[] |binário |Valor AMQP |
| string |string |Valor AMQP |
| System.Collections.IList |list |Valor AMQP: incluídas na coleção de itens só podem ser aquelas definidas nesta tabela. |
| System.Array |array |Valor AMQP: incluídas na coleção de itens só podem ser aquelas definidas nesta tabela. |
| System.Collections.IDictionary |map |Valor AMQP: incluídas na coleção de itens só podem ser aquelas definidas nesta tabela. Nota: apenas as chaves de cadeia de caracteres são suportadas. |
| URI |Descrito a cadeia de caracteres (consulte a tabela seguinte) |Valor AMQP |
| DateTimeOffset |Descrito há muito tempo (consulte a tabela seguinte) |Valor AMQP |
| TimeSpan |Descrito há muito tempo (consulte o seguinte) |Valor AMQP |
| Transmitir em fluxo |binário |Dados de AMQP (podem ser vários). As secções de dados contêm os bytes não processados que leem o objeto Stream. |
| Outro objeto |binário |Dados de AMQP (podem ser vários). Contém o binário serializado do objeto que utiliza o DataContractSerializer ou um serializador fornecido pela aplicação. |

| Tipo de .NET | AMQP mapeada descrito tipo | Notas |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Diferenças de comportamento

Existem algumas pequenas diferenças no comportamento da API .NET do Service Bus ao utilizar o AMQP, em comparação comparada o protocolo predefinido:

* O [OperationTimeout] [ OperationTimeout] propriedade é ignorada.
* `MessageReceiver.Receive(TimeSpan.Zero)` é implementado como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* A concluir as mensagens por tokens de bloqueio só pode ser feita pelos recetores mensagem recebida inicialmente as mensagens.

## <a name="control-amqp-protocol-settings"></a>Controlar as definições do protocolo do AMQP

O [.NET APIs](/dotnet/api/) expor várias definições para controlar o comportamento do protocolo AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Controla o crédito inicial aplicado a uma ligação. A predefinição é 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Controles, o tamanho máximo de quadro AMQP disponíveis durante a negociação em ligação abra tempo. A predefinição é 65,536 bytes.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Se estiverem batchable transferências, este valor determina o atraso máximo para o envio de dispositions. Herdada por remetentes/destinatários por predefinição. Remetente/destinatário individual pode substituir a predefinição, o que é 20 milissegundos.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Controla se as ligações AMQP são estabelecidas ao longo de uma ligação SSL. A predefinição é **true**.

## <a name="next-steps"></a>Passos Seguintes

Pronto para saber mais? Visite as seguintes ligações:

* [Descrição geral do AMQP no Service Bus]
* [Guia do protocolo AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Descrição geral do AMQP no Service Bus]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md

