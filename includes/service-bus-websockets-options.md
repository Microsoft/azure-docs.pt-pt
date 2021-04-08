---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693409"
---
A opção de protocolo AMQP-over-WebSockets passa por cima do porto TCP 443 tal como a API HTTP/REST, mas é de outra forma funcionalmente idêntica com AMQP simples. Esta opção tem uma latência inicial de ligação um pouco mais elevada devido a veleiros extra e um pouco mais sobrecarga como compensação pela partilha da porta HTTPS. Se este modo for selecionado, a porta TCP 443 é suficiente para a comunicação. As seguintes opções permitem selecionar o modo amQP simples ou amqp WebSockets:

| Linguagem | Opção   |
| -------- | ----- |
| .NET     | [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) propriedade com [TransportType.Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype) ou [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) com [com.microsoft.azure.servicebus.primitives.TransportType.AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) ou [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Nó  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) tem um `webSocket` argumento de construtor. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) com [TransportType.Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) ou [TransportType.AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |