---
title: Esquema de assinatura da Rede de Eventos Azure
description: Este artigo descreve as propriedades para subscrever um evento com a Azure Event Grid. Esquema de subscrição da Grelha de Eventos.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720763"
---
# <a name="event-grid-subscription-schema"></a>Esquema de subscrição da Grelha de Eventos

Para criar uma subscrição da Rede de Eventos, envia um pedido para a operação de subscrição do Evento Criar. Utilize o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Por exemplo, para criar uma subscrição `examplestorage` de evento `examplegroup`para uma conta de armazenamento nomeada num grupo de recursos chamado , use o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

O nome de Subscrição de Eventos deve ter 3-64 caracteres de comprimento e só pode conter a-z, A-Z, 0-9 e "-". O artigo descreve as propriedades e o esquema para o corpo do pedido.
 
## <a name="event-subscription-properties"></a>Propriedades de subscrição de eventos

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| destino | objeto | O objeto que define o ponto final. |
| filter | objeto | Um campo opcional para filtrar os tipos de eventos. |

### <a name="destination-object"></a>objeto de destino

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ponto finalType | string | O tipo de ponto final para a subscrição (webhook/HTTP, Event Hub ou fila). | 
| endpointUrl | string | O URL de destino para eventos nesta subscrição do evento. | 

### <a name="filter-object"></a>objeto de filtro

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| incluídoEventTypes | array | Combine quando o tipo de evento na mensagem do evento é uma correspondência exata com um destes nomes do tipo evento. Levanta um erro quando o nome do evento não corresponde aos nomes do tipo de evento registado para a fonte do evento. O padrão corresponde a todos os tipos de eventos. |
| assuntosComeçaCom | string | Um filtro de pré-fixação para o campo de assunto na mensagem do evento. A cadeia padrão ou vazia corresponde a todas. | 
| subjectEndsWith | string | Um filtro de correspondência de sufixo para o campo de assunto na mensagem do evento. A cadeia padrão ou vazia corresponde a todas. |
| isSubjectCaseSensitive | string | Controla a correspondência sensível a casos para filtros. |


## <a name="example-subscription-schema"></a>Esquema de assinatura de exemplo

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

* Para uma introdução à Grelha de Eventos, veja [o que é a Grelha de Eventos?](overview.md)
