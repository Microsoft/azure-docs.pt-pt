---
title: Grupo de recursos Azure como fonte de Grade de Eventos
description: Descreve as propriedades que são fornecidas para eventos de grupo de recursos com Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ed01bfdb67d9b8a3dd5875ec3fd8c6edf8922520
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105919"
---
# <a name="azure-resource-group-as-an-event-grid-source"></a>Grupo de recursos Azure como fonte de Grade de Eventos

Este artigo fornece as propriedades e esquema para eventos de grupo de recursos.Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md).

As subscrições azure e os grupos de recursos emitem os mesmos tipos de eventos. Os tipos de eventos estão relacionados com alterações de recursos ou ações. A principal diferença é que os grupos de recursos emitem eventos para recursos dentro do grupo de recursos, e as subscrições da Azure emitem eventos para recursos em toda a subscrição.

Os eventos de recursos são criados para as operações PUT, PATCH, POST e DELETE que são enviadas para `management.azure.com` . As operações get não criam eventos. As operações enviadas para o plano de dados `myaccount.blob.core.windows.net` (como) não criam eventos. Os eventos de ação fornecem dados de eventos para operações como a listagem das chaves para um recurso.

Quando subscreve eventos para um grupo de recursos, o seu ponto final recebe todos os eventos para esse grupo de recursos. Os eventos podem incluir eventos que você deseja ver, como atualizar uma máquina virtual, mas também eventos que talvez não sejam importantes para si, como escrever uma nova entrada na história da implementação. Pode receber todos os eventos no seu ponto final e escrever código que processa os eventos que pretende lidar. Ou, pode configurar um filtro ao criar a subscrição do evento.

Para lidar programaticamente com eventos, pode classificar eventos olhando para o `operationName` valor. Por exemplo, o seu ponto final do evento só pode processar eventos para operações iguais `Microsoft.Compute/virtualMachines/write` ou `Microsoft.Storage/storageAccounts/write` .

O assunto do evento é o ID de recursos do recurso que é o alvo da operação. Para filtrar eventos para um recurso, forneça esse ID de recurso ao criar a subscrição do evento.  Para filtrar por um tipo de recurso, utilize um valor em seguinte formato: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`


## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Os grupos de recursos emitem eventos de gestão do Azure Resource Manager, como quando um VM é criado ou uma conta de armazenamento é eliminada.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Levantado quando a ação sobre o recurso é cancelada. |
| Microsoft.Resources.ResourceActionFailure | Levantado quando a ação sobre o recurso falha. |
| Microsoft.Resources.ResourceActionSuccess | Levantado quando a ação sobre os recursos é bem sucedida. |
| Microsoft.Resources.ResourceDeleteCancel | Levantado quando a operação de eliminação é cancelada. Este evento acontece quando uma implementação do modelo é cancelada. |
| Microsoft.Resources.ResourceDeleteFailure | Levantado quando a operação de eliminação falha. |
| Microsoft.Resources.ResourceDeleteSuccess | Levantado quando a operação de eliminação tiver sucesso. |
| Microsoft.Resources.ResourceWriteCancel | Levantado quando a operação de criação ou atualização é cancelada. |
| Microsoft.Resources.ResourceWriteFailure | Levantado quando a operação de criação ou atualização falha. |
| Microsoft.Resources.ResourceWriteSuccess | Levantado quando a operação de criação ou atualização for bem sucedida. |

### <a name="example-event"></a>Exemplo evento

O exemplo a seguir mostra o esquema de um evento **ResourceWriteSuccess.** O mesmo esquema é utilizado para **eventos ResourceWriteFailure** e **ResourceWriteCancel** com valores diferentes para `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

O exemplo a seguir mostra o esquema de um evento **ResourceDeleteSuccess.** O mesmo esquema é utilizado para **eventos ResourceDeleteFailure** e **ResourceDeleteCancel** com valores diferentes para `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

O exemplo a seguir mostra o esquema para um evento **ResourceActionSuccess.** O mesmo esquema é utilizado para **eventos ResourceAcilure** e **ResourceActionCancel** com valores diferentes para `eventType` .

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}" 
}]
```

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Dados de eventos de grupo de recursos. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| autorização | objeto | A autorização solicitada para a operação. |
| sinistros | objeto | As propriedades das reclamações. Para mais informações, consulte [a especificação JWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | string | Uma operação de identificação para resolução de problemas. |
| httpRequest | objeto | Os detalhes da operação. Este objeto só está incluído na atualização de um recurso existente ou na eliminação de um recurso. |
| recursoProvider | string | O fornecedor de recursos para a operação. |
| recursosUri | string | O URI do recurso na operação. |
| operationName | string | A operação que foi feita. |
| status | string | O estado da operação. |
| subscriptionId | string | A identificação de assinatura do recurso. |
| inquilinoId | string | A identificação do inquilino do recurso. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Tutorial: monitorize alterações de máquinas virtuais com Azure Event Grid e Aplicações Lógicas](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza as alterações numa máquina virtual e envia e-mails sobre essas alterações. |
| [Azure CLI: subscreva eventos para um grupo de recursos](./scripts/event-grid-cli-resource-group.md)| Guião de amostra que subscreve eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [Azure CLI: subscreva eventos para um grupo de recursos e filtre por um recurso](./scripts/event-grid-cli-resource-group-filter.md) | O script da amostra que subscreve eventos para um grupo de recursos e filtra eventos para um recurso. |
| [PowerShell: subscreva eventos para um grupo de recursos](./scripts/event-grid-powershell-resource-group.md) | Guião de amostra que subscreve eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [PowerShell: subscreva eventos para um grupo de recursos e filtre por um recurso](./scripts/event-grid-powershell-resource-group-filter.md) | O script da amostra que subscreve eventos para um grupo de recursos e filtra eventos para um recurso. |
| [Modelo de gestor de recursos: subscrição de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Subscreve eventos para uma subscrição ou grupo de recursos Azure. Envia eventos para um WebHook. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
