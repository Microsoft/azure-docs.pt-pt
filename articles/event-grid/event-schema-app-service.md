---
title: Azure App Service como fonte de Grade de Eventos
description: Este artigo descreve como usar o Azure App Service como fonte de eventos da Grade de Eventos. Fornece o esquema e links para artigos tutoriais e como-a-para-
author: jasonfreeberg
ms.topic: conceptual
ms.date: 03/06/2021
ms.author: jafreebe
ms.openlocfilehash: 404fac634a628da49dee72b10b52785820fe1bf6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443782"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service como fonte de grade de eventos

Este artigo fornece as propriedades e esquema para eventos do Azure App Service. Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md). Também lhe dá uma lista de partidas rápidas e tutoriais para usar o Azure App Service como fonte de evento.

## <a name="available-event-types"></a>Tipos de eventos disponíveis

O Azure App Service emite os seguintes tipos de eventos

|    Tipo de Evento                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites. BackupOperationStarted             |    Desencadeado quando uma cópia de segurança começou                             |
|    Microsoft.Web/sites. BackupOperationCompleted           |    Desencadeado quando uma cópia de segurança está concluída                           |
|    Microsoft.Web/sites. BackupOperaçãoFailed              |    Desencadeado quando um backup falhou                              |
|    Microsoft.Web/sites. RestaurarOperationStarted            |    Desencadeado quando uma restauração de uma cópia de segurança começou        |
|    Microsoft.Web/sites. RestauraçãoOperaçãoCompletada          |    Desencadeado quando uma restauração de uma cópia de segurança está concluída      |
|    Microsoft.Web/sites. RestauraçãoOperaçãoFailed             |    Desencadeado quando uma restauração de uma cópia de segurança falhou         |
|    Microsoft.Web/sites. SlotSwapStarted                    |    Desencadeado quando uma troca de slots começou                          |
|    Microsoft.Web/sites. SlotSwapCompleted                  |    Desencadeado quando uma troca de slot está concluída                      |
|    Microsoft.Web/sites. SlotSwapFailed                     |    Desencadeado quando uma troca de slot falhou                           |
|    Microsoft.Web/sites. SlotSwapWithPreviewStarted         |    Desencadeado quando uma troca de slot com pré-visualização começou           |
|    Microsoft.Web/sites. SlotSwapWithPreviewCancelled       |    Desencadeado quando uma troca de slot com pré-visualização foi cancelada    |
|    Microsoft.Web/sites. AppUpdated.Restarted.Recomeçado               |    Desencadeado quando um site foi reiniciado                      |
|    Microsoft.Web/sites. AppUpdated.Stop                 |    Desencadeado quando um site foi parado                          |
|    Microsoft.Web/sites. AppUpdated.ChangedAppSettings      |    Desencadeado quando as definições de aplicações de um site mudaram             |
|    Microsoft.Web/serverfarms. AppServicePlanUpdated        |    Desencadeado quando um Plano de Serviço de Aplicações é atualizado                 |

## <a name="properties-common-to-all-events"></a>Propriedades comuns a todos os eventos

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)
Quando um evento é desencadeado, o serviço De Grelha de Eventos envia dados sobre esse evento para subscrever o ponto final.
Esta secção contém um exemplo de como seriam esses dados para cada evento. Cada evento tem os seguintes dados de alto nível:

|     Propriedade          |     Tipo     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    cadeia (de carateres)    |    Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor.                                      |
|    `subject`            |    string    |    Caminho definido pela editora para o tema do evento.                                                                                              |
|    `eventType`          |    string    |    Um dos tipos de eventos registados para esta fonte de evento.                                                                                  |
|    `eventTime`          |    string    |    O tempo que o evento é gerado com base no tempo UTC do fornecedor.                                                                         |
|    `id`                 |    string    |    Identificador único para o evento.                                                                                                            |
|    `data`               |    objeto    |    Dados do evento de armazenamento de bolhas.                                                                                                                    |
|    `dataVersion`        |    string    |    A versão do esquema do objeto de dados. O publicador define a versão do esquema.                                                          |
|    `metadataVersion`    |    string    |    A versão de esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. A Grade de Eventos fornece este valor.    |

# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

Quando um evento é desencadeado, o serviço De Grelha de Eventos envia dados sobre esse evento para subscrever o ponto final.
Esta secção contém um exemplo de como seriam esses dados para cada evento. Cada evento tem os seguintes dados de alto nível:

|     Propriedade          |     Tipo     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    cadeia (de carateres)    |    Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor.                                      |
|    `subject`            |    string    |    Caminho definido pela editora para o tema do evento.                                                                                              |
|    `type`          |    string    |    Um dos tipos de eventos registados para esta fonte de evento.                                                                                  |
|    `time`          |    string    |    O tempo que o evento é gerado com base no tempo UTC do fornecedor.                                                                         |
|    `id`                 |    string    |    Identificador único para o evento.                                                                                                            |
|    `data`               |    objeto    |    Dados do evento de armazenamento de bolhas.                                                                                                                    |
| `specversion` | string | Versão de especificação de esquemas CloudEvents. |

---

## <a name="example-events"></a>Exemplo de eventos

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe de ação na app                                                                                       |
|    `action`                  |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que tinha este evento                                                                          |
|    `clientRequestId`         |    string    |    O ID de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    `correlationRequestId`    |    string    |    O ID de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    `requestId`               |    string    |    O ID do pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    `address`                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    `verb`                    |    string    |    VERBO HTTP desta operação                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestaurarOperaçãoOperatória, RestauraçãoCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe de ação na app                                                                                       |
|    `action`                  |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que tinha este evento                                                                          |
|    `clientRequestId`         |    string    |    O ID de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    `correlationRequestId`    |    string    |    O ID de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    `requestId`               |    string    |    O ID do pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    `address`                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    `verb`                    |    string    |    VERBO HTTP desta operação                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe de ação na app                                                                                       |
|    `action`                 |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que tinha este evento                                                                          |
|    `clientRequestId`         |    string    |    O ID de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    `correlationRequestId`    |    string    |    O ID de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|   `requestId`               |    string    |    O ID do pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    `address`                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    `verb`                    |    string    |    VERBO HTTP desta operação                                                                                       |
|    `sourceSlot`              |    string    |    A ranhura de origem da permuta                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe de ação na app                                                                                       |
|    `action`                 |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que tinha este evento                                                                          |
|    `clientRequestId`         |    string    |    O ID de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    `correlationRequestId`    |    string    |    O ID de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    `requestId`               |    string    |    O ID do pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    `address`                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    `verb`                    |    string    |    VERBO HTTP desta operação                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stop, AppUpdated.ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados tem as seguintes propriedades:

|    Propriedade                |    Tipo      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    objeto    |    Detalhe de ação na app                                                                                       |
|    `action`                  |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                    |    string    |    nome do site que tinha este evento                                                                          |
|    `clientRequestId`         |    string    |    O ID de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    `correlationRequestId`    |    string    |    O ID de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    `requestId`               |    string    |    O ID do pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    `address`                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    `verb`                    |    string    |    VERBO HTTP desta operação                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Esquema de eventos do Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Esquema de eventos da cloud](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

O objeto de dados tem as seguintes propriedades:

|    Propriedade                         |    Tipo      |    Description                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    objeto    |    Detalhe de ação no plano de serviço de aplicações                                                                          |
|    `stampKind`                        |    string    |    Tipo de ambiente onde o plano de serviço de aplicações é                                                                     |
|    `action`                           |    string    |    Tipo de ação no plano de serviço de aplicações                                                                            |
|    `status`                           |    string    |    Estado da operação no plano de serviço de aplicações                                                                   |
|    `sku`                              |    objeto    |    sku do plano de serviço de aplicações                                                                                       |
|    `name`                             |    string    |    nome do plano de serviço de aplicações                                                                                      |
|    `Tier`                             |    string    |    nível do plano de serviço de aplicações                                                                                      |
|    `Size`                             |    string    |    tamanho do plano de serviço de aplicações                                                                                      |
|    `Family`                           |    string    |    família do plano de serviço de aplicativos                                                                                        |
|    `Capacity`                         |    string    |    capacidade do plano de serviço de aplicações                                                                                      |
|    `action`                           |    string    |    Tipo de ação da operação                                                                                   |
|    `name`                             |    string    |    nome do site que tinha este evento                                                                          |
|    `clientRequestId`                  |    string    |    O ID de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    `correlationRequestId`             |    string    |    O ID de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    `requestId`                        |    string    |    O ID do pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    `address`                         |    string    |    HTTP solicitar URL desta operação                                                                                |
|    `verb`                             |    string    |    VERBO HTTP desta operação                                                                                       |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md)