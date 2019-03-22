---
title: Evento de postagem para um tópico personalizado do Azure Event Grid
description: Descreve como publicar um evento para um tópico personalizado do Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: fc8877ed23b408ea041de67018a71cc203c5e8c0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182409"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publicar em tópico personalizado do Azure Event Grid

Este artigo descreve como publicar um evento para um tópico personalizado. Mostra o formato dos dados post e eventos. O [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) só se aplica a postagens que corresponde ao formato esperado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Ponto Final

Ao enviar o HTTP POST para um tópico personalizado, utilize o formato do URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Por exemplo, é um URI válido: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Para obter o ponto final para um tópico personalizado com a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Para obter o ponto final para um tópico personalizado com o Azure PowerShell, utilize:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Cabeçalho

Na solicitação, incluem um valor de cabeçalho chamado `aeg-sas-key` que contém uma chave para a autenticação.

Por exemplo, é um valor de cabeçalho válido `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Para obter a chave para um tópico personalizado com a CLI do Azure, utilize:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Para obter a chave para um tópico personalizado com o PowerShell, utilize:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dados do evento

Para tópicos personalizados, os dados de nível superior contém os mesmos campos que eventos padrão definido pelo recurso. Uma dessas propriedades é uma propriedade de dados que contém as propriedades exclusivas para o tópico personalizado. Como publicador de eventos, determinar as propriedades para esse objeto de dados. Utilize o esquema seguinte:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Para obter uma descrição destas propriedades, consulte [esquema de eventos do Azure Event Grid](event-schema.md). Eventos para um tópico do event grid no lançamento, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz está limitado a 64 KB.

Por exemplo, um esquema de dados de evento válido é:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Resposta

Após o lançamento para o ponto de final do tópico, receber uma resposta. A resposta é um código de resposta HTTP padrão. Algumas respostas comuns são:

|Resultado  |Resposta  |
|---------|---------|
|Êxito  | 200 OK  |
|Dados de eventos tem um formato incorreto | 400 pedido inválido |
|Chave de acesso inválido | 401 não autorizado |
|Ponto final incorreto | 404 Não Encontrado |
|Matriz ou evento excede os limites de tamanho | Payload 413 demasiado grande |

Para erros, o corpo da mensagem tem o seguinte formato:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre a monitorização de entregas de eventos, consulte [entrega de mensagens do Monitor Event Grid](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [Event Grid segurança e autenticação](security-authentication.md).
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
