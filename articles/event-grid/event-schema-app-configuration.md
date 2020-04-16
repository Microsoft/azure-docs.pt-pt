---
title: Configuração da aplicação Azure como fonte da grelha de eventos
description: Este artigo descreve como usar a Configuração da App Azure como fonte de evento sinuoso. Fornece o esquema e ligações ao tutorial e aos artigos de como fazer.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393436"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Configuração de aplicativo sinuoso como fonte da Grelha de Eventos
Este artigo fornece as propriedades e esquemas para eventos de configuração de aplicações do Azure. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md). Também lhe dá uma lista de arranques rápidos e tutoriais para usar a Configuração de Aplicações Azure como fonte de evento.

## <a name="event-grid-event-schema"></a>Esquema de evento sinuoso do evento Da Grelha de Eventos

### <a name="available-event-types"></a>Tipos de eventos disponíveis

A Configuração da Aplicação Azure emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModificado | Criado quando um valor-chave é criado ou substituído. |
| Microsoft.AppConfiguration.KeyValueDeleted | Levantado quando um valor-chave é eliminado. |

### <a name="example-event"></a>Evento de exemplo

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
 
### <a name="event-properties"></a>Propriedades do evento

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

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)

|Título | Descrição |
|---------|---------|
| [Reaja aos eventos de configuração de aplicações do Azure utilizando a Grelha de Eventos](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração da configuração da app Azure com grelha de eventos. |
| [Quickstart: encaminha eventos de configuração de aplicações azure para um ponto final personalizado com O ClI Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de configuração de aplicações Azure para um WebHook. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
* Para uma introdução ao trabalho com eventos de configuração de aplicações do Azure, consulte eventos de configuração de [aplicações Route Azure - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 