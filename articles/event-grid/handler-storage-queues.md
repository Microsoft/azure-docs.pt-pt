---
title: Fila de armazenamento como manipulador de eventos para eventos azure event grid
description: Descreve como pode usar as filas de armazenamento Azure como manipuladores de eventos para eventos da Rede de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598543"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Fila de armazenamento como manipulador de eventos para eventos azure event grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **o Azure Queue Storage** é um deles. 

Utilize o Armazenamento de **Fila** para receber eventos que precisam de ser puxados. Pode utilizar o armazenamento da fila quando tiver um processo de longa duração que demora muito tempo a responder. Ao enviar eventos para o armazenamento de fila, a aplicação pode puxar e processar eventos na sua própria agenda.

## <a name="tutorials"></a>Tutoriais
Consulte o seguinte tutorial para um exemplo de utilização do armazenamento de fila como manipulador de eventos. 

|Título  |Descrição  |
|---------|---------|
| [Quickstart: enverede eventos personalizados para armazenamento de fila Azure com Azure CLI e Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo de manipuladores de [eventos](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
