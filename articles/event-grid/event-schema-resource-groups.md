---
title: Esquema de evento de grupo de recursos da Azure Event Grid
description: Descreve as propriedades que estão fornecidas para eventos de grupo de recursos com a Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 6cbfc06f380d7c4818ca82e858c23bb18849fb7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561698"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Esquema de evento sinuoso do evento Azure Event Grid para grupos de recursos

Este artigo fornece as propriedades e esquemas para eventos de grupo de recursos.Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md).

As assinaturas azure e os grupos de recursos emitem os mesmos tipos de eventos. Os tipos de eventos estão relacionados com alterações ou ações de recursos. A principal diferença é que os grupos de recursos emitem eventos para recursos dentro do grupo de recursos, e as subscrições do Azure emitem eventos de recursos em toda a subscrição.

Os eventos de recursos são criados para operações `management.azure.com`PUT, PATCH, POST e DELETE que são enviadas para . As operações get não criam eventos. As operações enviadas `myaccount.blob.core.windows.net`para o avião de dados (como) não criam eventos. Os eventos de ação fornecem dados de eventos para operações como a listagem das chaves para um recurso.

Ao subscrever eventos para um grupo de recursos, o seu ponto final recebe todos os eventos para esse grupo de recursos. Os eventos podem incluir eventos que você quer ver, como atualizar uma máquina virtual, mas também eventos que talvez não sejam importantes para você, como escrever uma nova entrada no histórico de implementação. Você pode receber todos os eventos no seu ponto final e escrever código que processa os eventos que você quer lidar. Ou, pode definir um filtro ao criar a subscrição do evento.

Para lidar programáticamente com os eventos, `operationName` pode classificar eventos olhando para o valor. Por exemplo, o seu ponto final do evento `Microsoft.Compute/virtualMachines/write` só `Microsoft.Storage/storageAccounts/write`pode processar eventos para operações iguais ou .

O assunto do evento é a identificação de recursos do recurso que é o alvo da operação. Para filtrar eventos para um recurso, forneça esse ID de recurso ao criar a subscrição do evento.  Para filtrar por um tipo de recurso, utilize um valor no seguinte formato:`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Para obter uma lista de scripts e tutoriais de amostras, consulte a fonte do [evento do grupo Resource](event-sources.md#resource-groups).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

Os grupos de recursos emitem eventos de gestão do Azure Resource Manager, como quando um VM é criado ou uma conta de armazenamento é eliminada.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Levantada saem quando a ação sobre os recursos é cancelada. |
| Microsoft.Resources.ResourceActionFailure | Levantada quando a ação sobre os recursos falha. |
| Microsoft.Resources.ResourceActionSuccess | Levantada quando a ação sobre os recursos é bem sucedida. |
| Microsoft.Resources.ResourceDeleteCancel | Levantada quando a eliminação da operação é cancelada. Este evento acontece quando uma implantação do modelo é cancelada. |
| Microsoft.Resources.ResourceDeleteFailure | Levantada sabotada quando a eliminação da operação falha. |
| Microsoft.Resources.ResourceDeleteSuccess | Levantada quando a eliminação da operação é bem sucedida. |
| Microsoft.Resources.ResourceWriteCancel | Levantada quando a operação de criação ou atualização é cancelada. |
| Microsoft.Resources.ResourceWriteFailure | O funcionamento da criação ou atualização falha. |
| Microsoft.Resources.ResourceWriteSuccess | Criada quando a operação de criação ou atualização é bem sucedida. |

## <a name="example-event"></a>Evento de exemplo

O exemplo seguinte mostra o esquema para um evento **ResourceWriteSuccess.** O mesmo esquema é utilizado para **eventos ResourceWriteFailure** e `eventType` **ResourceWriteCancel** com valores diferentes para .

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

O exemplo seguinte mostra o esquema para um evento **ResourceDeleteSuccess.** O mesmo esquema é utilizado para **eventos DeexcluseDeEeeeeos** com **valores** diferentes para `eventType`.

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

O exemplo que se segue mostra o esquema para um evento **ResourceActionSuccess.** O mesmo esquema é utilizado para **eventos ResourceActionFailure** e `eventType` **ResourceActionCancel** com valores diferentes para .

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

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é reelegível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento do grupo de recursos. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| autorização | objeto | A autorização solicitada para a operação. |
| reivindicações | objeto | As propriedades das reclamações. Para mais informações, consulte a [especificação jWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | string | Uma identidade de operação para resolução de problemas. |
| httpRequest | objeto | Os detalhes da operação. Este objeto só é incluído quando atualizar um recurso existente ou apagar um recurso. |
| recursosFornecedor | string | O fornecedor de recursos para a operação. |
| recursosUri | string | O URI do recurso na operação. |
| operationName | string | A operação que foi levada. |
| status | string | O estado da operação. |
| subscriptionId | string | A identificação de subscrição do recurso. |
| inquilinoId | string | A identificação do inquilino do recurso. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
