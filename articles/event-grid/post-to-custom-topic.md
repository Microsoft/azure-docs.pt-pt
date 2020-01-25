---
title: Post event to custom Azure Event Grid topic
description: Este artigo descreve como publicar um evento num tópico personalizado. Mostra o formato dos dados do post e do evento.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721562"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Post to custom topic for Azure Event Grid

Este artigo descreve como publicar um evento num tópico personalizado. Mostra o formato dos dados do post e do evento. O [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) aplica-se apenas a publicações que correspondam ao formato esperado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Ponto Final

Ao enviar o HTTP POST para um tópico personalizado, utilize o formato URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Por exemplo, um URI válido é: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Para obter o ponto final para um tópico personalizado com o Azure CLI, use:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Para obter o ponto final para um tópico personalizado com o Azure PowerShell, use:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Cabeçalho

No pedido, inclua um valor cabeçalho chamado `aeg-sas-key` que contém uma chave para autenticação.

Por exemplo, um valor de cabeçalho válido é `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Para obter a chave para um tópico personalizado com o Azure CLI, use:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Para obter a chave para um tópico personalizado com powerShell, use:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dados do evento

Para tópicos personalizados, os dados de alto nível contêm os mesmos campos que os eventos definidos por recursos padrão. Uma dessas propriedades é uma propriedade de dados que contém propriedades únicas para o tópico personalizado. Como editor de eventos, determina as propriedades desse objeto de dados. Utilize o seguinte esquema:

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

Para obter uma descrição destas propriedades, consulte o esquema do [evento Azure Event Grid](event-schema.md). Ao postar eventos em um tópico da grade de eventos, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz é limitado a 64 KB (disponibilidade geral) ou 1 MB (versão prévia).

> [!NOTE]
> Um evento de tamanho de até 64 KB é coberto pela disponibilidade geral (GA) Contrato de Nível de Serviço (SLA). O suporte para um evento de tamanho de até 1 MB está atualmente em visualização. Eventos acima de 64 KB são cobrados em incrementos de 64 KB. 

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

Depois de publicar no ponto final do tópico, recebe uma resposta. A resposta é um código de resposta HTTP padrão. Algumas respostas comuns são:

|Resultado  |Resposta  |
|---------|---------|
|Êxito  | 200 OK  |
|Os dados do evento têm formato incorreto | 400 solicitação inadequada |
|Chave de acesso inválida | 401 não autorizado |
|Ponto final incorreto | 404 Não Encontrado |
|Matriz ou evento excede limites de tamanho | 413 Carga útil demasiado grande |

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

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre como monitorar entregas de eventos, consulte [monitorar a entrega de mensagens na grade de eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [segurança e autenticação da grade de eventos](security-authentication.md).
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
