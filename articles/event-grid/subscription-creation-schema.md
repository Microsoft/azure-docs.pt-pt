---
title: Esquema de subscrições do Azure Event Grid
description: Descreve as propriedades de assinatura de um evento com o Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 6129c7f498ce6c52fce4266f693c6a304642f8c3
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845471"
---
# <a name="event-grid-subscription-schema"></a>Esquema de subscrições do Event Grid

Para criar uma subscrição do Event Grid, envie um pedido para a operação de subscrição de evento de criar. Utilize o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Por exemplo criar uma subscrição de evento para uma conta de armazenamento com o nome `examplestorage` num grupo de recursos com o nome `examplegroup`, utilize o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

O nome de subscrição de evento tem de ter 3 e 64 carateres de comprimento e só pode conter a-z, A-Z, 0-9, e "-". O artigo descreve as propriedades e o esquema para o corpo do pedido.
 
## <a name="event-subscription-properties"></a>Propriedades de subscrição de evento

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| destination | object | O objeto que define o ponto final. |
| filter | object | Um campo opcional para filtrar os tipos de eventos. |

### <a name="destination-object"></a>objeto de destino

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| endpointType | string | O tipo de ponto final para a subscrição (webhook/HTTP, Hub de eventos ou fila). | 
| endpointUrl | string | O URL de destino para eventos nesta subscrição de evento. | 

### <a name="filter-object"></a>objeto de filtro

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| includedEventTypes | array | Correspondência quando o tipo de evento na mensagem de evento é uma correspondência exata com um destes nomes de tipo de evento. Gera um erro quando o nome do evento não coincide com os nomes de tipos de eventos registrados para a origem do evento. Predefinição corresponde a todos os tipos de eventos. |
| subjectBeginsWith | string | Uma correspondência de prefixo filtro para o campo do requerente de eventos da mensagem. A predefinição ou uma cadeia vazia corresponde a todos. | 
| subjectEndsWith | string | Uma correspondência de sufixo de filtro para o campo do requerente de eventos da mensagem. A predefinição ou uma cadeia vazia corresponde a todos. |
| isSubjectCaseSensitive | string | Controlos de maiúsculas e minúsculas correspondente para filtros. |


## <a name="example-subscription-schema"></a>Esquema de subscrições de exemplo

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Event Grid, consulte [o que é o Event Grid?](overview.md)
