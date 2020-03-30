---
title: Esquema de configuração de evento de app Azure Grid Azure
description: Descreve as propriedades que estão fornecidas para eventos de configuração de aplicações azure com grelha de eventos Azure
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735785"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Esquema de evento de evento azure para configuração de app Azure

Este artigo fornece as propriedades e esquemas para eventos de configuração de aplicações do Azure. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md).

Para obter uma lista de scripts e tutoriais de amostras, consulte a fonte do evento de configuração da [aplicação do Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

A Configuração da Aplicação Azure emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModificado | Criado quando um valor-chave é criado ou substituído. |
| Microsoft.AppConfiguration.KeyValueDeleted | Levantado quando um valor-chave é eliminado. |

## <a name="example-event"></a>Evento de exemplo

O exemplo que se segue mostra o esquema de um evento modificado de valor-chave: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento eliminado de valor-chave é semelhante: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento de configuração de aplicativos. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| key | string | A chave do valor-chave que foi modificado ou eliminado. |
| label | string | A etiqueta, se houver, do valor-chave que foi modificado ou eliminado. |
| etag | string | Para `KeyValueModified` o etag do novo valor-chave. Para `KeyValueDeleted` o etag do valor-chave que foi eliminado. |
 
## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
* Para uma introdução ao trabalho com eventos de configuração de aplicações do Azure, consulte eventos de configuração de [aplicações Route Azure - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 