---
title: Tópicos personalizados na Grelha de Eventos Azure
description: Descreve tópicos personalizados na Grelha de Eventos Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86113790"
---
# <a name="custom-topics-in-azure-event-grid"></a>Tópicos personalizados na Grelha de Eventos Azure
Um tópico da grelha de eventos fornece um ponto final onde a fonte envia eventos. A editora cria o tópico da grelha de eventos e decide se uma fonte de evento precisa de um tópico ou mais de um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para responder a determinados tipos de eventos, os subscritores decidem quais os tópicos a subscrever.

**Os tópicos personalizados** são a aplicação e tópicos de terceiros. Quando cria ou lhe é atribuído acesso a um tópico personalizado, verá esse tópico personalizado na sua subscrição. 

Ao desenhar a sua aplicação, tem flexibilidade ao decidir quantos tópicos criar. Para soluções de grande porza, crie um **tópico personalizado** para cada categoria **de eventos relacionados.** Por exemplo, considere uma aplicação que envia eventos relacionados com a modificação de contas de utilizador e com o processamento de encomendas. É pouco provável que qualquer processador de eventos queira as duas categorias de eventos. Crie dois tópicos personalizados e deixe que os processadores de eventos subscrevam aquele que for do seu interesse. Para pequenas soluções, você pode preferir enviar todos os eventos para um único tópico. Os subscritores do evento podem filtrar os tipos de eventos que quiserem.

## <a name="event-schema"></a>Esquema de eventos
Para obter uma visão detalhada do esquema do evento, consulte [o esquema do evento Azure Event Grid](event-schema.md). Para tópicos personalizados, o editor do evento determina o objeto de **dados.** Os dados de alto nível devem ter os mesmos campos que os eventos definidos pelos recursos padrão.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

As secções seguintes fornecem links para tutoriais para criar tópicos personalizados usando os modelos do portal Azure, CLI, PowerShell e Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Tutoriais do portal Azure
|Título  |Descrição  |
|---------|---------|
| [Quickstart: criar e encaminhar eventos personalizados com o portal Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Quickstart: encaminhar eventos personalizados para o armazenamento da Fila Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |
| [Como: postar para tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento para um tópico personalizado. |


## <a name="azure-cli-tutorials"></a>Tutoriais do Azure CLI
|Título  |Descrição  |
|---------|---------|
| [Quickstart: criar e encaminhar eventos personalizados com O Azure CLI](custom-event-quickstart.md) | Mostra como usar o Azure CLI para enviar eventos personalizados. |
| [Azure CLI: crie o tópico personalizado da Grade de Eventos](./scripts/event-grid-cli-create-custom-topic.md)|O guião da amostra que cria um tópico personalizado. O guião recupera o ponto final e uma chave.|
| [Azure CLI: subscreva eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Guião de amostra que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|

## <a name="azure-powershell-tutorials"></a>Tutoriais Azure PowerShell
|Título  |Descrição  |
|---------|---------|
| [Quickstart: criar e encaminhar eventos personalizados com a Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar a Azure PowerShell para enviar eventos personalizados. |
| [PowerShell: crie o tópico personalizado da Grade de Eventos](./scripts/event-grid-powershell-create-custom-topic.md)|O guião da amostra que cria um tópico personalizado. O guião recupera o ponto final e uma chave.|
| [PowerShell: subscreva eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Guião de amostra que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|

## <a name="arm-template-tutorials"></a>Tutoriais de modelo arm
|Título  |Descrição  |
|---------|---------|
| [Modelo de gestor de recursos: tópico personalizado e ponto final WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo de Gestor de Recursos que cria um tópico personalizado e subscrição para esse tópico personalizado. Envia eventos para um WebHook. |
| [Modelo de gestor de recursos: tópico personalizado e ponto final de Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Tópicos de sistema](system-topics.md)
- [Domínios](event-domains.md)