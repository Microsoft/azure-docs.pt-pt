---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.custom: fasttrack-edit
ms.openlocfilehash: 2c3eb2423c308d512bb880525cb15fef2ffbdb2c
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89304097"
---
Esta tabela mostra as encadernações suportadas nas principais versões do tempo de funcionamento das Funções Azure:


| Tipo | 1.x | 2.x e superior<sup>1</sup> | Acionador | Input | Saída |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Armazenamento de blobs](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [BD do Cosmos](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Dapr](https://github.com/dapr/azure-functions-extension)<sup>3</sup>             | |✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| |✔|
| [Hubs de Eventos](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP & webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Hub IoT](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Kafka](https://github.com/azure/azure-functions-kafka-extension)<sup>2</sup>             | |✔|✔| |✔|
| [Aplicações Móveis](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Hubs de Notificação](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Armazenamento de filas](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)<sup>2</sup>             | |✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Armazenamento de tabelas](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Temporizador](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> A partir da versão 2.x, todas as ligações, exceto HTTP e Timer, devem ser registadas. Consulte [as extensões de ligação do Registo](../articles/azure-functions/functions-bindings-register.md).

<sup>2</sup> Os gatilhos não são suportados no plano de consumo. Requer [gatilhos orientados para o tempo de execução](../articles/azure-functions/functions-networking-options.md#premium-plan-with-virtual-network-triggers).

<sup>3</sup> Suportado apenas em Kubernetes, IoT Edge e outros modos auto-hospedados apenas.
