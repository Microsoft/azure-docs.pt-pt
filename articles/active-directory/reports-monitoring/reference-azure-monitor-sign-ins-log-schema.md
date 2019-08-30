---
title: Azure Active Directory o esquema de log de entrada no Azure Monitor | Microsoft Docs
description: Descrever o esquema de logon de entrada do Azure AD para uso no Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cc07ac2644ac9f97146e980a1961b9b84e7c561
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127053"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretar o esquema de logs de entrada do Azure AD no Azure Monitor

Este artigo descreve o esquema de logon de entrada do Azure Active Directory (AD do Azure) no Azure Monitor. A maioria das informações relacionadas a entradas é fornecida no atributo *Properties* do `records` objeto.


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
| Time | A data e a hora, em UTC. |
| ResourceId | Esse valor é não mapeado e você pode ignorar esse campo com segurança.  |
| OperationName | Para entradas, esse valor é sempre a *atividade de entrada*. |
| OperationVersion | A versão da API REST solicitada pelo cliente. |
| Category | Para entradas, esse valor sempre é *Sign*-in. | 
| TenantId | O GUID do locatário que está associado aos logs. |
| ResultType | O resultado da operação de entrada pode ser *êxito* ou *falha*. | 
| ResultSignature | Contém o código de erro, se houver, para a operação de entrada. |
| ResultDescription | Fornece a descrição do erro para a operação de entrada. |
| riskDetail | riskDetail | Fornece o "motivo" por trás de um estado específico de um usuário arriscado, uma conexão ou uma detecção de risco. Os valores possíveis são: `none` `userPerformedSecuredPasswordChange`, `adminGeneratedTemporaryPassword`,, `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe`,, `aiConfirmedSigninSafe`,, ,,`unknownFutureValue`. `userPassedMFADrivenByRiskBasedPolicy` `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised` O valor `none` significa que nenhuma ação foi executada no usuário ou entre até agora. <br>**Nota:** Os detalhes dessa propriedade exigem uma licença Azure AD Premium P2. Outras licenças retornam o `hidden`valor. |
| riskEventTypes | riskEventTypes | Tipos de detecção de risco associados à entrada. Os valores possíveis são: `unlikelyTravel`, `anonymizedIPAddress` `maliciousIPAddress` `unfamiliarFeatures` ,,`malwareInfectedIPAddress`,, `suspiciousIPAddress`, ,`investigationsThreatIntelligence`, e`unknownFutureValue`. `leakedCredentials` `generic` |
| riskLevelAggregated | riskLevel | Nível de risco agregado. Os valores possíveis são: `none`, `low`, `medium`, `high` `hidden`, e `unknownFutureValue`. O valor `hidden` significa que o usuário ou a entrada não foi habilitado para Azure ad Identity Protection. **Nota:** Os detalhes desta propriedade estão disponíveis somente para clientes Azure AD Premium P2. Todos os outros clientes serão retornados `hidden`. |
| riskLevelDuringSignIn | riskLevel | Nível de risco durante a entrada. Os valores possíveis são: `none`, `low`, `medium`, `high` `hidden`, e `unknownFutureValue`. O valor `hidden` significa que o usuário ou a entrada não foi habilitado para Azure ad Identity Protection. **Nota:** Os detalhes desta propriedade estão disponíveis somente para clientes Azure AD Premium P2. Todos os outros clientes serão retornados `hidden`. |
| risco | risco | Relata o status do usuário arriscado, de entrada ou de uma detecção de risco. Os valores possíveis são: `none` `remediated`, `confirmedSafe`,, `dismissed`,,,. `atRisk` `confirmedCompromised` `unknownFutureValue` |
| DurationMs |  Esse valor é não mapeado e você pode ignorar esse campo com segurança. |
| CallerIpAddress | O endereço IP do cliente que fez a solicitação. | 
| CorrelationId | O GUID opcional que é passado pelo cliente. Esse valor pode ajudar a correlacionar operações do lado do cliente com operações do lado do servidor e é útil quando você está controlando logs que abrangem serviços. |
| identidade | A identidade do token que foi apresentado quando você fez a solicitação. Pode ser uma conta de usuário, uma conta do sistema ou uma entidade de serviço. |
| Nível | Fornece o tipo de mensagem. Para auditoria, é sempre informativo. |
| Location | Fornece o local da atividade de entrada. |
| properties | Lista todas as propriedades que estão associadas a entradas. Para obter mais informações, consulte [Microsoft Graph referência de API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Esse esquema usa os mesmos nomes de atributo que o recurso de entrada, para facilitar a leitura.

## <a name="next-steps"></a>Passos Seguintes

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Interpretar o esquema dos registos de auditoria no Azure Monitor)
* [Leia mais sobre os logs de diagnóstico do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
