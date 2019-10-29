---
title: Arquiteturas orientadas a eventos no Edge – grade de eventos do Azure no IoT Edge
description: Use a grade de eventos do Azure como um módulo em IoT Edge para eventos de encaminhamento entre módulos, dispositivos de borda e a nuvem.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: e03429ed3df5bd3518d5e5194bd842b9a4f290ba
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991947"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>O que é a grade de eventos do Azure no Azure IoT Edge?
A grade de eventos em IoT Edge traz a potência e a flexibilidade da grade de eventos do Azure para a borda de todos os cenários de pub/sub e orientados a eventos. Crie tópicos, publique eventos e assine vários destinos se eles forem módulos no mesmo dispositivo, outros dispositivos de borda ou serviços na nuvem.

Como na nuvem, a grade de eventos em IoT Edge módulo manipula o roteamento, a filtragem e a entrega confiável de eventos em escala. Filtre eventos para garantir que apenas eventos relevantes sejam enviados a manipuladores de eventos diferentes usando filtros de cadeia de caracteres avançados, numéricos e boolianos. A lógica de repetição garante que o evento alcance o módulo de destino, o dispositivo de borda ou o serviço de nuvem, mesmo que ele não esteja disponível no momento da publicação. Ele permite que você use a grade de eventos em IoT Edge como um mecanismo avançado de armazenamento e encaminhamento.

A grade de eventos em IoT Edge dá suporte ao CloudEvents v 1.0 e a esquemas de evento personalizados. Ele também dá suporte à mesma semântica de publicação e assinatura que a grade de eventos na nuvem para facilitar a interoperabilidade com o Azure e terceiros.

Este artigo fornece uma visão geral da grade de eventos do Azure no IoT Edge. Para obter instruções detalhadas sobre como usar esse módulo no Edge, consulte [publicar, assinar eventos localmente](pub-sub-events-webhook-local.md). 

![Grade de eventos em IoT Edge modelo de fontes e manipuladores](../media/edge-overview/functional-model.png)

Esta imagem mostra algumas das maneiras como você pode usar a grade de eventos em IoT Edge e não é uma lista abrangente de funcionalidades com suporte.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Quando usar a grade de eventos em IoT Edge

A grade de eventos na IoT Edge é criada para fornecer um modelo de eventos confiável, uniforme e fácil de usar para a criação de arquiteturas controladas por eventos entre a borda e a nuvem.

A grade de eventos na IoT Edge é criada com uma área de superfície de tempo de execução simétrica para o serviço de nuvem do Azure, para que você possa usar os mesmos eventos e chamadas de API onde for necessário. Seja você o pub/sub na nuvem, na borda ou entre os dois, a grade de eventos em IoT Edge agora pode ser sua solução de passagem.

Use a grade de eventos em IoT Edge para disparar fluxos de trabalho simples entre módulos. Por exemplo, crie um tópico e publique eventos "blob de armazenamento criado" do seu módulo de armazenamento para o tópico. Agora você pode assinar uma ou várias funções ou módulos personalizados para esses tópicos.

Estenda sua funcionalidade entre dispositivos de borda. Se você estiver publicando eventos de módulo de BLOB e quiser usar a potência computacional de vários dispositivos próximos por borda, crie assinaturas entre dispositivos.

Por fim, conecte-se à nuvem. Se os eventos do módulo de blob forem sincronizados periodicamente com a nuvem, use a computação maior disponível na nuvem ou envie dados processados para cima, crie assinaturas de serviço de nuvem adicionais.

A grade de eventos no IoT Edge fornece uma arquitetura de eventos desacoplada flexível e confiável.

## <a name="event-sources"></a>Origens de eventos

Assim como na nuvem, a grade de eventos no IoT Edge permite a integração direta entre os módulos para criar arquiteturas controladas por eventos. No momento, os eventos podem ser enviados para a grade de eventos no IoT Edge de:

* Armazenamento de Blobs do Azure no IoT Edge
* Fontes de CloudEvents
* Módulos personalizados & contêineres via HTTP POST

## <a name="event-handlers"></a>Processadores de eventos

A grade de eventos no IoT Edge é criada para enviar eventos para qualquer lugar que você desejar. No momento, há suporte para os seguintes destinos:

* Outros módulos, incluindo o Hub IoT, funções e módulos personalizados
* Outros dispositivos de borda
* Serviços hospedados na nuvem, incluindo grade de eventos do Azure e Azure Functions
* Webhooks

## <a name="supported-environments"></a>Ambientes com suporte
Atualmente, há suporte para ambientes do Windows 64 bits, Linux de 64 bits e ARM de 32 bits.

## <a name="concepts"></a>Conceitos

Há cinco conceitos na grade de eventos do Azure que permitem que você comece:

* **Eventos** – o que aconteceu.
* **Origens de evento** — onde o evento ocorreu.
* **Tópicos** — o ponto de extremidade onde os Publicadores enviam eventos.
* **Assinaturas de evento** — o ponto de extremidade ou mecanismo interno para rotear eventos, às vezes para mais de um manipulador. As subscrições também são utilizadas pelos processadores para filtrar inteligentemente os eventos recebidos.
* **Manipuladores de eventos** — o aplicativo ou serviço que reage ao evento.

## <a name="cost"></a>Custo

A grade de eventos em IoT Edge é gratuita durante a visualização pública.

## <a name="issues"></a>Problemas
Relatar quaisquer problemas com o uso da grade de eventos em IoT Edge em [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues).

## <a name="next-steps"></a>Passos seguintes

* [Publicar, assinar eventos localmente](pub-sub-events-webhook-local.md)
* [Publicar, assinar eventos na nuvem](pub-sub-events-webhook-cloud.md)
* [Encaminhar eventos para a nuvem da grade de eventos](forward-events-event-grid-cloud.md)
* [Encaminhar eventos para IoTHub](forward-events-iothub.md)
* [Reagir a eventos do armazenamento de BLOBs localmente](react-blob-storage-events-locally.md)