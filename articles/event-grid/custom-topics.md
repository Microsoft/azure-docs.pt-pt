---
title: Tópicos personalizados na Grelha de Eventos Azure
description: Descreve tópicos personalizados na Grelha de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 166a48d74d32c8b0a3a59310e693ea96ada29116
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394361"
---
# <a name="custom-topics-in-azure-event-grid"></a>Tópicos personalizados na Grelha de Eventos Azure
Um tópico da grelha de eventos fornece um ponto final onde a fonte envia eventos. A editora cria o tópico da grelha do evento e decide se uma fonte de evento precisa de um tópico ou mais do que um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para responder a certos tipos de eventos, os subscritores decidem quais os tópicos a subscrever.

**Os tópicos personalizados** são aplicação e tópicos de terceiros. Quando cria ou lhe é atribuído acesso a um tópico personalizado, verá esse tópico personalizado na sua subscrição. 

Ao desenhar a sua aplicação, tem flexibilidade ao decidir quantos tópicos criar. Para grandes soluções, crie um **tópico personalizado** para cada categoria **de eventos relacionados.** Por exemplo, considere uma aplicação que envia eventos relacionados com a modificação de contas de utilizador e com o processamento de encomendas. É pouco provável que qualquer processador de eventos queira as duas categorias de eventos. Crie dois tópicos personalizados e deixe que os processadores de eventos subscrevam aquele que for do seu interesse. Para soluções pequenas, talvez prefira enviar todos os eventos para um único tópico. Os subscritores do evento podem filtrar os tipos de eventos que querem.

## <a name="event-schema"></a>Esquema de eventos
Para uma visão detalhada do esquema do evento, consulte o esquema do [evento Azure Event Grid](event-schema.md). Para tópicos personalizados, a editora de eventos determina o objeto de **dados.** Os dados de alto nível devem ter os mesmos campos que os eventos definidos por recursos padrão.

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

As seguintes secções fornecem links para tutoriais para criar tópicos personalizados usando modelos de portal Azure, CLI, PowerShell e Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Tutoriais do portal Azure
|Título  |Descrição  |
|---------|---------|
| [Quickstart: criar e encaminhar eventos personalizados com o portal Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Quickstart: encaminha eventos personalizados para o armazenamento da Fila Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |
| [Como: post arnde ao tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento num tópico personalizado. |


## <a name="azure-cli-tutorials"></a>Tutoriais Azure CLI
|Título  |Descrição  |
|---------|---------|
| [Quickstart: criar e encaminhar eventos personalizados com o Azure CLI](custom-event-quickstart.md) | Mostra como usar o Azure CLI para enviar eventos personalizados. |
| [Azure CLI: crie tópico personalizado da Grelha de Eventos](./scripts/event-grid-cli-create-custom-topic.md)|O script da amostra que cria um tópico personalizado. O guião recupera o ponto final e uma chave.|
| [Azure CLI: subscreva eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Sample script que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|

## <a name="azure-powershell-tutorials"></a>Tutoriais Azure PowerShell
|Título  |Descrição  |
|---------|---------|
| [Quickstart: criar e encaminhar eventos personalizados com a Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar o Azure PowerShell para enviar eventos personalizados. |
| [PowerShell: criar tópico personalizado da Grelha de Eventos](./scripts/event-grid-powershell-create-custom-topic.md)|O script da amostra que cria um tópico personalizado. O guião recupera o ponto final e uma chave.|
| [PowerShell: subscreva eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Sample script que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|

## <a name="arm-template-tutorials"></a>Tutoriais de modelo arm
|Título  |Descrição  |
|---------|---------|
| [Modelo de Gestor de Recursos: tópico personalizado e ponto final do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo de Gestor de Recursos que cria um tópico personalizado e subscrição para esse tópico personalizado. Envia eventos para um WebHook. |
| [Modelo de Gestor de Recursos: tópico personalizado e ponto final do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Tópicos de sistema](system-topics.md)
- [Domínios](event-domains.md)