---
title: Interprete o esquema de registo de auditoria do Azure Ative Directory no Monitor Azure [ Microsoft Docs
description: Descreva o esquema de registo de auditoria da AD Azure para utilização no Monitor Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9f58b213e50a021651f35112a48d8f74ae59571
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68987944"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interprete o esquema de registos de auditoria da AD Azure no Monitor Azure (pré-visualização)

Este artigo descreve o esquema de registo de auditoria azure ative directory (Azure AD) no Azure Monitor. Cada entrada individual de registo é armazenada como texto e formatada como uma bolha JSON, como mostram os dois exemplos seguintes: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Descrições de campo e propriedade

| Nome do campo | Descrição |
|------------|-------------|
| hora       | A data e a hora (UTC). |
| operationName | O nome da operação. |
| operationVersion | A versão REST API que é solicitada pelo cliente. |
| categoria | Atualmente, a *Auditoria* é o único valor suportado. |
| inquilinoId | O inquilino GUID que está associado aos registos. |
| resultType | O resultado da operação. O resultado pode ser *Sucesso* ou *Fracasso.* |
| resultSignature |  Este campo está desmapeado, e podeignorá-lo com segurança. | 
| resultDescription | Uma descrição adicional do resultado, quando disponível. | 
| durationMs |  Este campo está desmapeado, e podeignorá-lo com segurança. |
| callerIpAddress | O endereço IP do cliente que fez o pedido. | 
| correlationId | Um GUID opcional que é passado pelo cliente. Pode ajudar a relacionar as operações do lado do cliente com operações do lado do servidor e é útil quando está a rastrear registos que abrangem serviços. |
| identidade | A identidade do símbolo que foi apresentado quando fez o pedido. A identidade pode ser uma conta de utilizador, conta de sistema ou diretor de serviço. |
| nível | O tipo de mensagem. Para registos de auditoria, o nível é sempre *Informativo*. |
| localização | A localização do centro de dados. |
| propriedades | Lista as propriedades suportadas que estão relacionadas com um registo de auditoria. Para mais informações, consulte a mesa ao lado. | 

<br>

| Nome da propriedade | Descrição |
|---------------|-------------|
| Categoria AuditEvent | O tipo de evento de auditoria. Pode ser Gestão de *Utilizadores,* Gestão de *Aplicações,* ou outro tipo.|
| Tipo de Identidade | O tipo pode ser *Aplicação* ou *Utilizador.* |
| Tipo de Operação | O tipo pode ser *Adicionar,* *Atualizar,* *Eliminar*. ou *Outros.* |
| Tipo de recurso-alvo | Especifica o tipo de recurso-alvo em que a operação foi executada. O tipo pode ser *Aplicação,* *Utilizador,* *Função,* *Política* | 
| Nome de recurso-alvo | O nome do recurso alvo. Pode ser um nome de aplicação, um nome de papel, um nome principal do utilizador ou um nome principal do serviço. |
| alvos adicionais | Lista quaisquer propriedades adicionais para operações específicas. Por exemplo, para uma operação de atualização, os valores antigos e os novos valores estão listados no *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Passos seguintes

* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
* [Registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Perguntas mais frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)