---
title: Evento de post para personalizar o tópico da Grelha de Eventos Azure
description: Este artigo descreve como publicar um evento para um tópico personalizado. Mostra o formato dos dados do post e do evento.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 197d8eb1963300bc6576e664c7c3fd470cf70bb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108265"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Post para tópico personalizado para Azure Event Grid

Este artigo descreve como publicar um evento para um tópico personalizado. Mostra o formato dos dados do post e do evento. O [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) aplica-se apenas a publicações que correspondam ao formato esperado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Ponto final

Ao enviar o HTTP POST para um tópico personalizado, utilize o formato URI: `https://<topic-endpoint>?api-version=2018-01-01` .

Por exemplo, um URI válido é: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .

Para obter o ponto final para um tópico personalizado com Azure CLI, use:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Para obter o ponto final para um tópico personalizado com a Azure PowerShell, use:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Cabeçalho

No pedido, inclua um valor de cabeçalho nomeado `aeg-sas-key` que contém uma chave para a autenticação.

Por exemplo, um valor de cabeçalho válido é `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` .

Para obter a chave para um tópico personalizado com Azure CLI, use:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Para obter a chave para um tópico personalizado com PowerShell, use:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dados do evento

Para tópicos personalizados, os dados de alto nível contêm os mesmos campos que os eventos definidos por recursos padrão. Uma dessas propriedades é uma propriedade de dados que contém propriedades únicas ao tópico personalizado. Como editor de eventos, você determina as propriedades para esse objeto de dados. Utilize o seguinte esquema:

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

Para uma descrição destas propriedades, consulte [o esquema de eventos da Azure Event Grid](event-schema.md). Ao publicar eventos num tópico de grelha de evento, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz é limitado a 64 KB (Disponibilidade Geral) ou 1 MB (pré-visualização).

> [!NOTE]
> Um evento de tamanho até 64 KB é coberto pelo Acordo de Nível de Serviço (SLA) de Disponibilidade Geral (GA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Os eventos com mais de 64 KB são carregados em incrementos de 64 KB. 

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
|Success  | 200 OK  |
|Os dados do evento têm formato incorreto | 400 Pedido Incorreto |
|Chave de acesso inválida | 401 Não Autorizado |
|Ponto final incorreto | 404 Não Encontrado |
|Matriz ou evento excede limites de tamanho | 413 Carga Útil Demasiado Grande |

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

* Para obter informações sobre a monitorização das entregas de eventos, consulte [a entrega de mensagens monitor a Grelha de Eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
