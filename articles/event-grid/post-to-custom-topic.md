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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721562"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Post to custom topic for Azure Event Grid

Este artigo descreve como publicar um evento num tópico personalizado. Mostra o formato dos dados do post e do evento. O [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) aplica-se apenas a publicações que correspondam ao formato esperado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Ponto Final

Ao enviar o HTTP POST para um tópico `https://<topic-endpoint>?api-version=2018-01-01`personalizado, utilize o formato URI: .

Por exemplo, um URI `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`válido é: .

Para obter o ponto final para um tópico personalizado com o Azure CLI, use:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Para obter o ponto final para um tópico personalizado com o Azure PowerShell, use:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Cabeçalho

No pedido, inclua um `aeg-sas-key` valor cabeçalho nomeado que contém uma chave para autenticação.

Por exemplo, um valor `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`de cabeçalho válido é .

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

Para obter uma descrição destas propriedades, consulte o esquema do [evento Azure Event Grid](event-schema.md). Ao publicar eventos para um tópico de grelha de eventos, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz está limitado a 64 KB (Disponibilidade Geral) ou 1 MB (pré-visualização).

> [!NOTE]
> Um evento de tamanho até 64 KB é coberto pelo Acordo de Nível de Serviço de Disponibilidade Geral (GA) (SLA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Eventos superiores a 64 KB são carregados em incrementos de 64 KB. 

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
|Os dados do evento têm formato incorreto | 400 Mau Pedido |
|Chave de acesso inválida | 401 Não autorizado |
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

* Para obter informações sobre a monitorização das entregas de eventos, consulte a entrega de [mensagens Monitor Event Grid](monitor-event-delivery.md).
* Para mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
