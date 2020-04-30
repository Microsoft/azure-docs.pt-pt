---
title: Esquema de log in in in Azure Monitor [ Log-in] Microsoft Docs
description: Descreva o sinal de AD Azure em esquema de log para uso no Monitor Azure
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
ms.openlocfilehash: 6d7c9713f27643e792ea381e1a2419cbc4b67a99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129206"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interprete o esquema de login da AD Azure no Monitor Azure

Este artigo descreve o login de login Azure Ative (Azure AD) no Azure Monitor. A maior parte da informação relacionada com os *Properties* sign-ins `records` é fornecida sob o atributo de Propriedades do objeto.


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


## <a name="field-descriptions"></a>Descrições dos campos

| Nome do campo | Descrição |
|------------|-------------|
| Hora | A data e a hora, na UTC. |
| ResourceId | Este valor não está mapeado, e pode ignorar com segurança este campo.  |
| OperationName | Para inscrições, este valor é sempre *atividade de início de sessão.* |
| OperaçãoVersão | A versão REST API que é solicitada pelo cliente. |
| Categoria | Para inscrições, este valor é sempre *SignIn*. | 
| TenantId | O inquilino GUID que está associado aos registos. |
| Tipo de resultados | O resultado da operação de inscrição pode ser *Sucesso* ou *Falha*. | 
| ResultSignature | Contém o código de erro, se houver, para a operação de iniciar sessão. |
| Descrição do resultado | Fornece a descrição do erro para a operação de inscrição. |
| riscoDetalhe | riscoDetalhe | Fornece a "razão" por detrás de um estado específico de utilizador, de sessão ou de deteção de riscos. Os valores `none`possíveis `userPerformedSecuredPasswordChange` `userPerformedSecuredPasswordReset`são: `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser` `adminConfirmedSigninSafe` `adminGeneratedTemporaryPassword` `adminConfirmedSigninCompromised`, `unknownFutureValue`, , , . O `none` valor significa que até ao momento não foi realizada qualquer ação no utilizador ou no sessão. <br>**Nota:** Os detalhes para esta propriedade requerem uma licença Azure AD Premium P2. Outras licenças `hidden`devolvem o valor. |
| riscoEventTypes | riscoEventTypes | Tipos de deteção de risco associados ao inserição. Os valores `unlikelyTravel`possíveis `maliciousIPAddress` `unfamiliarFeatures`são: `suspiciousIPAddress` `leakedCredentials`, `investigationsThreatIntelligence` `malwareInfectedIPAddress` `anonymizedIPAddress` `generic`, `unknownFutureValue`, , , e . |
| riscoLevelAgregado | riscoN | Nível de risco agregado. Os valores `none`possíveis `high`são: `unknownFutureValue`, `low` `medium`, e `hidden`. O `hidden` valor significa que o utilizador ou o início de sessão não foram ativados para a Proteção de Identidade AD Azure. **Nota:** Os detalhes para este imóvel só estão disponíveis para clientes Azure AD Premium P2. Todos os outros `hidden`clientes serão devolvidos. |
| riscoLevelDuringSignIn | riscoN | Nível de risco durante o inessão. Os valores `none`possíveis `high`são: `unknownFutureValue`, `low` `medium`, e `hidden`. O `hidden` valor significa que o utilizador ou o início de sessão não foram ativados para a Proteção de Identidade AD Azure. **Nota:** Os detalhes para este imóvel só estão disponíveis para clientes Azure AD Premium P2. Todos os outros `hidden`clientes serão devolvidos. |
| riscoEstado | riscoEstado | Relatórios estado do utilizador arriscado, de iniciar sessão ou de uma deteção de risco. Os valores `none`possíveis são: `confirmedCompromised` `unknownFutureValue` `confirmedSafe`, `remediated`, `dismissed` `atRisk`, , . |
| DuraçãoMs |  Este valor não está mapeado, e pode ignorar com segurança este campo. |
| CallerIpAddress | O endereço IP do cliente que fez o pedido. | 
| CorrelationId | O GUID opcional que é passado pelo cliente. Este valor pode ajudar a relacionar as operações do lado do cliente com operações do lado do servidor, e é útil quando você está rastreando registos que abrangem serviços. |
| Identidade | A identidade do símbolo que foi apresentado quando fez o pedido. Pode ser uma conta de utilizador, conta de sistema ou diretor de serviço. |
| Nível | Fornece o tipo de mensagem. Para auditoria, é sempre *informacional.* |
| Localização | Fornece a localização da atividade de inscrição. |
| Propriedades | Lista todas as propriedades associadas a inscrições. Para mais informações, consulte a [Referência API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Este esquema usa os mesmos nomes de atributos que o recurso de entrada, para a legibilidade.

## <a name="next-steps"></a>Passos seguintes

* [Interpretar esquemas de registos de auditoria no Monitor Azure](reference-azure-monitor-audit-log-schema.md)
* [Ler mais sobre os registos da plataforma Azure](../../azure-monitor/platform/platform-logs-overview.md)
