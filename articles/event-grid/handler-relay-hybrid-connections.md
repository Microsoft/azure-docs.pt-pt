---
title: Relay Hybrid ligação como manipulador de eventos para eventos azure event grid
description: Descreve como pode utilizar ligações híbridas Azure Relay como manipuladores de eventos para eventos da Rede de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598431"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Relay Hybrid ligação como manipulador de eventos para eventos azure event grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **o Azure Relay** é um deles. 

Utilize **ligações híbridas** Azure Relay para enviar eventos para aplicações que estejam dentro de uma rede empresarial e não tenham um ponto final acessível ao público.

## <a name="tutorials"></a>Tutoriais
Consulte o seguinte tutorial para um exemplo de utilização de uma ligação híbrida Azure Relay como manipulador de eventos. 

|Título  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por uma aplicação de ouvintes. |

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo de manipuladores de [eventos](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 