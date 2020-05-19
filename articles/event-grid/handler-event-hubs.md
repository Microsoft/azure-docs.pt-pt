---
title: Hub de eventos como manipulador de eventos para eventos azure event grid
description: Descreve como pode usar os centros de eventos como manipuladores de eventos para eventos da Rede de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9ce4e7da51005dcb06c9df420d80f4d2c7b93e9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598368"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Hub de eventos como manipulador de eventos para eventos azure event grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma uma ação para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **o Azure Event Hubs** é um deles. 

Use Os Centros de **Eventos** quando a sua solução obtém eventos da Rede de Eventos mais rapidamente do que pode processar os eventos. Uma vez que os eventos estejam em um centro de eventos, a sua aplicação pode processar eventos a partir do centro de eventos na sua própria agenda. Pode escalar o processamento do seu evento para lidar com os eventos que estão a chegar.

## <a name="tutorials"></a>Tutoriais
Veja os exemplos seguintes: 

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Rota eventos personalizados para Hubs de Eventos Azure com Azure CLI](custom-event-to-eventhub.md) | Envia um evento personalizado para um centro de eventos para processamento por uma aplicação. |
| [Modelo de Gestor de Recursos: Crie um tópico personalizado da Grelha de Eventos e envie eventos para um hub de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |

## <a name="message-properties"></a>Propriedades da mensagem
Se utilizar um hub de **eventos** como manipulador de eventos para eventos a partir da Grelha de Eventos, detete os seguintes cabeçalhos de mensagem: 

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| aeg-subscrição-nome | Nome da subscrição do evento. |
| aeg-entrega-contagem | <p>Número de tentativas feitas para o evento.</p> <p>Exemplo: "1"</p> |
| aeg-tipo evento | <p>Tipo de evento.</p><p> Exemplo: "Microsoft.Storage.blobCreated"</p> | 
| aeg-metadados-versão | <p>Versão metadados do evento.</p> <p>Exemplo: "1".</p><p> Para o **event grid schema**, esta propriedade representa a versão metadados e para o esquema de **eventos em nuvem,** representa a **versão de especificação**. </p>|
| aeg-data-versão | <p>Versão de dados do evento.</p><p>Exemplo: "1".</p><p>Para o **event grid schema**, esta propriedade representa a versão de dados e para o esquema de **eventos na nuvem**, não se aplica.</p> |
| aeg-output-evento-id | ID do evento Event Grid. |


## <a name="next-steps"></a>Passos seguintes
Consulte o artigo de manipuladores de [eventos](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
