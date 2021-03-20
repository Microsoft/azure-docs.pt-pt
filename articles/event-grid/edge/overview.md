---
title: Arquiteturas impulsionadas por eventos no limite - Azure Event Grid em IoT Edge
description: Utilize a Grelha de Eventos Azure como módulo no IoT Edge para eventos avançados entre módulos, dispositivos de borda e a nuvem.
ms.topic: overview
ms.date: 07/08/2020
ms.openlocfilehash: 82a68f6ab32d8ad18c3af506c810b01d12cf794d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171521"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>O que é Azure Event Grid em Azure IoT Edge?
A Grelha de Eventos na IoT Edge traz a potência e a flexibilidade da Azure Event Grid para a borda. Crie tópicos, publique eventos e subscreva vários destinos quer sejam módulos no mesmo dispositivo, outros dispositivos de borda ou serviços na nuvem.

Tal como na nuvem, o módulo Event Grid on IoT Edge lida com o encaminhamento, filtragem e entrega fiável de eventos em escala. Filtrar eventos para garantir que apenas eventos relevantes são enviados para diferentes manipuladores de eventos usando filtros avançados de cordas, numéricos e booleanos. A lógica de relemisso garante que o evento atinge o destino alvo mesmo que não esteja disponível no momento da publicação. Permite-lhe utilizar a Grade de Eventos no IoT Edge como uma poderosa loja e mecanismo de avanço.

A Grade de Eventos no IoT Edge suporta tanto os esquemas de eventos CloudEvents v1.0 como os esquemas de eventos personalizados. Também suporta a mesma semântica pub/sub que a Grade de Eventos na nuvem para uma fácil interoperabilidade.

Este artigo fornece uma visão geral da Grelha de Eventos Azure em IoT Edge. Para instruções passo a passo para utilizar este módulo no limite, consulte [Publicar, subscrever eventos localmente.](pub-sub-events-webhook-local.md) 

![Grelha de eventos no modelo IoT Edge de fontes e manipuladores](../media/edge-overview/functional-model.png)

Esta imagem mostra algumas das formas de utilizar a Grade de Eventos no IoT Edge, e não é uma lista completa de funcionalidades suportadas.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Quando usar a Grade de Eventos na Borda IoT

A Grelha de Eventos no IoT Edge fornece um modelo de eventos fiável e fácil de usar entre a borda e a nuvem.

A Grade de Eventos no IoT Edge é construída com uma área de superfície de tempo de execução simétrica para o serviço de nuvem Azure, para que possa utilizar os mesmos eventos e chamadas API onde você precisar. Quer faça pub/sub na nuvem, na borda, ou entre os dois, a Grade de Eventos no IoT Edge pode agora ser a sua solução única.

Utilize a Grelha de Eventos no IoT Edge para desencadear fluxos de trabalho simples entre módulos. Por exemplo, crie um tópico e publique eventos de "storage blob criados" do seu módulo de armazenamento para o tópico. Agora pode subscrever uma ou várias funções ou módulos personalizados para esses tópicos.

Estenda a sua funcionalidade entre dispositivos de borda. Se estiver a publicar eventos de módulos blob e quiser utilizar a potência computacional de múltiplos dispositivos de borda, crie subscrições de dispositivos cruzados.

Finalmente, ligue-se à nuvem. Se os eventos do módulo blob forem sincronizados periodicamente na nuvem, use o maior cálculo disponível na nuvem ou envie dados processados, crie subscrições adicionais de serviços na nuvem.

A Grade de Eventos no IoT Edge proporciona uma arquitetura de eventos dissociada flexível e fiável.

## <a name="event-sources"></a>Origens de eventos

Tal como na nuvem, a Grade de Eventos no IoT Edge permite a integração direta entre módulos para construir arquiteturas orientadas para eventos. Atualmente, os eventos podem ser enviados para a Grade de Eventos em IoT Edge a partir de:

* Armazenamento de Blobs do Azure no IoT Edge
* Fontes cloudEvents
* Módulos personalizados & contentores via HTTP POST

## <a name="event-handlers"></a>Processadores de eventos

A Grade de Eventos no IoT Edge foi construída para enviar eventos para onde quiser. Atualmente, os seguintes destinos são suportados:

* Outros módulos, incluindo IoT Hub, funções e módulos personalizados
* Outros dispositivos de borda
* WebHooks
* Serviço de nuvem Azure Event Grid
* Hubs de Eventos
* Filas de Service Bus
* Tópicos de Service Bus
* Filas de Armazenamento

## <a name="supported-environments"></a>Ambientes apoiados
Atualmente, os ambientes windows 64-bit, Linux 64-bit e ARM 32 bits são suportados.

## <a name="concepts"></a>Conceitos

Existem cinco conceitos na Azure Event Grid que permitem começar:

* **Eventos** — O que aconteceu.
* **Fontes do** evento — Onde o evento teve lugar.
* **Tópicos** - O ponto final onde as editoras enviam eventos.
* **Subscrições de eventos** — O ponto final ou mecanismo incorporado para encaminhar eventos, por vezes para mais do que um manipulador. As subscrições também são utilizadas pelos processadores para filtrar inteligentemente os eventos recebidos.
* **Manipuladores de eventos** — A app ou serviço que reage ao evento.

## <a name="cost"></a>Custo

A Grelha de Eventos no IoT Edge é gratuita durante a pré-visualização pública.

## <a name="issues"></a>Questões
Informe quaisquer problemas com a utilização da Grade de Eventos no IoT Edge em [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .

## <a name="next-steps"></a>Passos seguintes

* [Publicar, subscrever eventos localmente](pub-sub-events-webhook-local.md)
* [Publicar, subscrever eventos na nuvem](pub-sub-events-webhook-cloud.md)
* [Encaminhar eventos para a nuvem de grade de eventos](forward-events-event-grid-cloud.md)
* [Reencaminhar eventos para ioTHub](forward-events-iothub.md)
* [Reagir aos eventos do Armazenamento de Blobs localmente](react-blob-storage-events-locally.md)