---
title: Arquiteturas orientadas para o evento no limite - Azure Event Grid na IoT Edge
description: Utilize a Grelha de Eventos Azure como um módulo no IoT Edge para eventos avançados entre módulos, dispositivos de borda e a nuvem.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76844666"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>O que é azure event grid em Azure IoT Edge?
A Grelha de Eventos em IoT Edge traz a potência e flexibilidade da Grelha de Eventos Azure para a borda. Crie tópicos, publique eventos e subscreva vários destinos, sejam módulos no mesmo dispositivo, outros dispositivos de borda ou serviços na nuvem.

Tal como na nuvem, o módulo Event Grid no IoT Edge trata do encaminhamento, filtragem e entrega fiável de eventos em escala. Filtrar eventos para garantir que apenas eventos relevantes são enviados para diferentes manipuladores de eventos usando filtros avançados de cordas, numéricos e booleanos. A lógica de retry garante que o evento chega ao destino alvo mesmo que não esteja disponível no momento da publicação. Permite-lhe utilizar a Grelha de Eventos no IoT Edge como uma loja poderosa e mecanismo para a frente.

A Grelha de Eventos no IoT Edge suporta tanto cloudEvents v1.0 como schemas de eventos personalizados. Também suporta a mesma semântica Pub/Sub como A Grelha de Eventos na nuvem para uma interoperabilidade fácil.

Este artigo fornece uma visão geral da Grelha de Eventos Azure em IoT Edge. Para instruções passo a passo para utilizar este módulo no limite, consulte [Publicar, subscrever eventos localmente](pub-sub-events-webhook-local.md). 

![Grelha de Eventos no modelo ioT Edge de fontes e manipuladores](../media/edge-overview/functional-model.png)

Esta imagem mostra algumas das formas de utilizar a Grelha de Eventos no IoT Edge, e não é uma lista completa de funcionalidades suportadas.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Quando usar a grelha de eventos na borda ioT

A Grelha de Eventos no IoT Edge fornece um modelo de evento sinuoso e fiável para entre a borda e a nuvem.

A Grelha de Eventos na IoT Edge é construída com uma área de superfície de tempo de execução simétrica para o serviço de nuvem Azure, para que possa usar os mesmos eventos e chamadas aPI onde precisar. Quer faça pub/sub na nuvem, na borda, ou entre os dois, a Grelha de Eventos no IoT Edge pode agora ser a sua única solução.

Utilize a grelha de eventos na borda ioT para desencadear fluxos de trabalho simples entre módulos. Por exemplo, crie um tópico e publique eventos "storage blob criados" do seu módulo de armazenamento para o tópico. Pode agora subscrever uma ou várias funções ou módulos personalizados para esses tópicos.

Aumente a sua funcionalidade entre dispositivos de borda. Se estiver a publicar eventos de módulos blob e pretender utilizar o poder computacional de vários dispositivos de borda, crie subscrições de dispositivos cruzados.

Finalmente, ligue-se à nuvem. Se os eventos do seu módulo blob forem sincronizados periodicamente à nuvem, use a maior computação disponível na nuvem, ou envie dados processados, crie subscrições adicionais de serviço na nuvem.

A Grelha de Eventos em IoT Edge oferece uma arquitetura de eventos flexíveis e fiáveis.

## <a name="event-sources"></a>Origens de eventos

Tal como na nuvem, a Grelha de Eventos no IoT Edge permite a integração direta entre módulos para construir arquiteturas orientadas para eventos. Atualmente, os eventos podem ser enviados para event grid em IoT Edge a partir de:

* Armazenamento de Blobs do Azure no IoT Edge
* Fontes cloudEvents
* Módulos personalizados & contentores via HTTP POST

## <a name="event-handlers"></a>Processadores de eventos

A Grelha de Eventos em IoT Edge foi construída para enviar eventos para onde quiser. Atualmente, os seguintes destinos são suportados:

* Outros módulos, incluindo Hub IoT, funções e módulos personalizados
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

Existem cinco conceitos na Grelha de Eventos Azure que lhe permitem começar:

* **Eventos** — O que aconteceu.
* **Fontes** do evento — Onde o evento teve lugar.
* **Tópicos** — O ponto final onde as editoras enviam eventos.
* **Assinaturas** de eventos — O ponto final ou mecanismo incorporado para encaminhar eventos, por vezes para mais do que um manipulador. As subscrições também são utilizadas pelos processadores para filtrar inteligentemente os eventos recebidos.
* **Manipuladores de eventos** — A app ou serviço que reage ao evento.

## <a name="cost"></a>Custo

A Grelha de Eventos no IoT Edge é gratuita durante a pré-visualização pública.

## <a name="issues"></a>Problemas
Informe quaisquer problemas com a utilização da Grelha de Eventos no IoT Edge em [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues).

## <a name="next-steps"></a>Passos seguintes

* [Publique, subscreva eventos localmente](pub-sub-events-webhook-local.md)
* [Publique, subscreva eventos na nuvem](pub-sub-events-webhook-cloud.md)
* [Eventos avançados para a nuvem da Grelha de Eventos](forward-events-event-grid-cloud.md)
* [Eventos avançados para ioTHub](forward-events-iothub.md)
* [Reagir aos eventos do Armazenamento de Blobs localmente](react-blob-storage-events-locally.md)