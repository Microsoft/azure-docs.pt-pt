---
title: Esquema de início de sessão no registo do Azure Active Directory no Azure Monitor | Documentos da Microsoft
description: Descrever o início de sessão do Azure AD no esquema de registo para utilização no Azure Monitor
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
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285201"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretar o esquema de início de sessão de registos do Azure AD no Azure Monitor

Este artigo descreve o esquema de início de sessão no registo do Azure Active Directory (Azure AD) no Azure Monitor. A maioria das informações relacionadas com inícios de sessão é fornecida através do *propriedades* atributo do `records` objeto.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Descrições de campo

| Nome do campo | Descrição |
|------------|-------------|
| Hora | A data e hora, em UTC. |
| ResourceId | Este valor é não mapeado, e pode ignorar este campo.  |
| OperationName | Para inícios de sessão, este valor é sempre *atividade de início de sessão*. |
| OperationVersion | A versão de REST API é solicitada pelo cliente. |
| Category | Para inícios de sessão, este valor é sempre *SignIn*. | 
| TenantId | O GUID associada os registos de inquilino. |
| ResultType | O resultado da operação de início de sessão pode ser *sucesso* ou *falha*. | 
| resultSignature | Contém o código de erro, se houver, para a operação de início de sessão. |
| ResultDescription | Fornece a descrição do erro para a operação de início de sessão. |
| DurationMs |  Este valor é não mapeado, e pode ignorar este campo.|
| CallerIpAddress | O endereço IP do cliente que efetuou o pedido. | 
| CorrelationId | O GUID opcional que é transmitido pelo cliente. Este valor pode ajudar a correlacionar operações do lado do cliente com as operações do lado do servidor e é útil quando está a controlar os registos que abrangem serviços. |
| Identidade | A identidade do token que foi apresentado quando efetuou o pedido. Pode ser uma conta de utilizador, a conta de sistema ou o principal de serviço. |
| Nível | Fornece o tipo de mensagem. Para auditoria, é sempre *informativo*. |
| Location | Disponibiliza a localização da atividade de início de sessão. |
| Propriedades | Apresenta uma lista de todas as propriedades que estão associadas a inícios de sessão. Para obter mais informações, consulte [referência da API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Esse esquema utiliza os mesmos nomes de atributo que o recurso de início de sessão, para facilitar a leitura.

## <a name="next-steps"></a>Passos Seguintes

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Interpretar o esquema dos registos de auditoria no Azure Monitor)
* [Leia mais sobre os registos de diagnóstico do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)