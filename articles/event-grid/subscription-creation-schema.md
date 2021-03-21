---
title: Esquema de subscrição da Azure Event Grid
description: Este artigo descreve as propriedades para subscrever um evento com a Azure Event Grid. Esquema de subscrição de Grelha de Evento.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: f6e33171cbba65cfeaca49ab6a8954be8bb89acb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199956"
---
# <a name="event-grid-subscription-schema"></a>Esquema de subscrição de Grade de Eventos

Para criar uma subscrição de Grade de Eventos, envie um pedido para a operação de subscrição do Evento Criar. Utilize o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Por exemplo, para criar uma subscrição de evento para uma conta de armazenamento nomeada `examplestorage` num grupo de recursos denominado , utilize o seguinte `examplegroup` formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

O nome de Subscrição do Evento deve ter 3-64 caracteres de comprimento e só pode conter a-z, A-Z, 0-9 e "-". O artigo descreve as propriedades e esquemas para o corpo do pedido.
 
## <a name="event-subscription-properties"></a>Propriedades de subscrição de eventos

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| destino | objeto | O objeto que define o ponto final. |
| filter | objeto | Um campo opcional para filtrar os tipos de eventos. |

### <a name="destination-object"></a>objeto de destino

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| endpointType | string | O tipo de ponto final para a subscrição (webhook/HTTP, Event Hub ou fila). | 
| endpointUrl | string | O URL de destino para eventos nesta subscrição do evento. | 

### <a name="filter-object"></a>objeto de filtro

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| incluieeventTypes | matriz | Corresponda quando o tipo de evento na mensagem do evento é uma correspondência exata com um destes nomes do tipo evento. Levanta um erro quando o nome do evento não corresponde aos nomes do tipo de evento registados para a fonte do evento. O predefinição corresponde a todos os tipos de eventos. |
| assuntoScom | string | Um filtro de pré-jogo para o campo de assunto na mensagem do evento. A corda padrão ou vazia combina com todas. | 
| AssuntoEndsWith | string | Um filtro de sufixo para o campo de assunto na mensagem do evento. A corda padrão ou vazia combina com todas. |
| isSubjectCaseSensitive | string | Controla a correspondência sensível à caixa para os filtros. |
| enableAdvancedFilteringOnArrays | boolean | Ativa a utilização de matrizes para chaves na filtragem avançada. Para obter mais informações, consulte [a filtragem Avançada.](event-filtering.md#advanced-filtering) |


## <a name="example-subscription-schema"></a>Exemplo esquema de subscrição

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

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grade de Eventos, veja [o que é a Grade de Eventos?](overview.md)
