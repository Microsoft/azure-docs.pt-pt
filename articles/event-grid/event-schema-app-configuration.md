---
title: Configuração de aplicativos Azure como fonte de grelha de evento
description: Este artigo descreve como usar a Configuração de Aplicações Azure como fonte de eventos da Grade de Eventos. Fornece o esquema e links para artigos tutoriais e como-a-para-
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: a64c6fead5e6d95ba11bc98d7e9a52e3021c3be2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366777"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Configuração de aplicativos Azure como fonte de grade de eventos
Este artigo fornece as propriedades e esquema para eventos de Configuração de Aplicações Azure. Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md). Também lhe dá uma lista de partidas rápidas e tutoriais para usar a Configuração de Aplicações Azure como fonte de evento.

## <a name="available-event-types"></a>Tipos de eventos disponíveis

A Azure App Configuration emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Levantado quando um valor-chave é criado ou substituído. |
| Microsoft.AppConfiguration.KeyValueDeleted | Levantado quando um valor-chave é eliminado. |

## <a name="example-event"></a>Exemplo evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)
O exemplo a seguir mostra o esquema de um evento modificado de valor chave: 

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
# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

O exemplo a seguir mostra o esquema de um evento modificado de valor chave: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueModified",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

O esquema para um evento eliminado de valor-chave é semelhante: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueDeleted",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Propriedades do evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)
Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| `topic` | cadeia (de carateres) | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `eventType` | string | Um dos tipos de eventos registados para esta origem de evento. |
| `eventTime` | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| `id` | string | Identificador único para o evento. |
| `data` | objeto | Dados do evento de configuração de aplicativos. |
| `dataVersion` | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| `metadataVersion` | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |


# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| `source` | cadeia (de carateres) | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `type` | string | Um dos tipos de eventos registados para esta origem de evento. |
| `time` | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| `id` | string | Identificador único para o evento. |
| `data` | objeto | Dados do evento de configuração de aplicativos. |
| `specversion` | string | Versão de especificação de esquemas CloudEvents. |

---

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| `key` | cadeia (de carateres) | A chave do valor-chave que foi modificada ou eliminada. |
| `label` | string | A etiqueta, se houver, do valor-chave que foi modificada ou eliminada. |
| `etag` | string | Para `KeyValueModified` o etag do novo valor-chave. Para `KeyValueDeleted` o etag do valor-chave que foi eliminado. |


## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)

|Título | Descrição |
|---------|---------|
| [Reagir a eventos de configuração de aplicativos Azure utilizando a Grade de Eventos](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração da Configuração de Aplicações Azure com grelha de eventos. |
| [Utilize a Grelha de Eventos para notificações de alteração de dados](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Saiba como utilizar as subscrições do evento de configuração de aplicações Azure para enviar eventos de modificação de valor chave para um ponto final web. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
* Para obter uma introdução ao trabalho com eventos de configuração de aplicações Azure, consulte [a Grelha de Eventos de Utilização para notificações de alteração de dados](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 