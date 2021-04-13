---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304427"
---
A opção de protocolo AMQP-over-WebSockets passa por cima do porto TCP 443 tal como a API HTTP/REST, mas é de outra forma funcionalmente idêntica com AMQP simples. Esta opção tem maior latência inicial de ligação devido a veleiros extra e um pouco mais de sobrecarga como compensação pela partilha da porta HTTPS. Se este modo for selecionado, a porta TCP 443 é suficiente para a comunicação. As seguintes opções permitem selecionar o modo AMQP WebSockets. 

| Linguagem | Opção   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Crie [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) usando um construtor que toma [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) como parâmetro. Definir [ServiceBusClientOptions.TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) to [ServiceBusTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) |
| .NET (Microsoft.Azure.ServiceBus)    | Ao criar objetos de cliente, utilize construtores que tomem [o TransportType,](/dotnet/api/microsoft.azure.servicebus.transporttype) [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)ou [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) como parâmetros. <p>Para a construção que toma `transportType` como parâmetro, desacorda o parâmetro [para TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Para o construtor que toma `ServiceBusConnection` como parâmetro, desaponte o [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) para [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Se `ServiceBusConnectionStringBuilder` utilizar, utilize construtores que lhe dêem a opção de especificar o `transportType` .</p> |
| Java (com.azure.messaging.servicebus)     | Ao criar clientes, desemote [o ServiceBusClientBuilder.transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) para [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com.microsoft.azure.servicebus)    | Ao criar clientes, coloque `transportType` em [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)  para [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| JavaScript  | Ao criar objetos clientes service bus, utilize a `webSocketOptions` propriedade em [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | Ao criar clientes de Service Bus, desempate [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) para [TransportType.AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) |

