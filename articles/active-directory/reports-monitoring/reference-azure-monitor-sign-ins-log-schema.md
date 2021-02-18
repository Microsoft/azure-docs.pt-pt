---
title: Esquema de registo de login em Azure Monitor | Microsoft Docs
description: Descreva o sinal AZURE AD no esquema de registo para utilização no Azure Monitor
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
ms.openlocfilehash: d912707a1f41a0c3063d6f3fb67aa6914bd2d390
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592306"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interprete o esquema de registos de registos AD AZure no Azure Monitor

Este artigo descreve o esquema de registo Azure Ative (Azure AD) no Azure Monitor. A maior parte da informação relacionada com as entradas é fornecida sob o atributo *Propriedades* do `records` objeto.


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

| Nome do campo | Description |
|------------|-------------|
| Hora | A data e a hora, na UTC. |
| ResourceId | Este valor não é mapeado, e você pode ignorar com segurança este campo.  |
| OperationName | Para os insi's, este valor é sempre *atividade de inscrição.* |
| OperationVersion | A versão REST API que é solicitada pelo cliente. |
| Categoria | Para iniciar sposições, este valor é sempre *SignIn*. | 
| TenantId | O inquilino GUID que está associado com os troncos. |
| ResultType | O resultado da operação de inscrição pode ser *sucesso* ou *fracasso*. | 
| ResultSignature | Contém o código de erro, se houver, para a operação de inscrição. |
| ResultDescription | Fornece a descrição do erro para a operação de inscrição. |
| riscoDetail | riscoDetail | Fornece a "razão" por trás de um estado específico de um utilizador arriscado, de iniciar sposição ou de uma deteção de riscos. Os valores possíveis são: `none` , , , , , , , , , `adminGeneratedTemporaryPassword` , , . `userPerformedSecuredPasswordChange` `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe` `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy` `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised` `unknownFutureValue` . O valor `none` significa que até ao momento não foi realizada qualquer ação no utilizador ou no pedido de inscrição. <br>**Nota:** Os detalhes desta propriedade requerem uma licença Azure AD Premium P2. Outras licenças devolvem o `hidden` valor. |
| tipos de risco | tipos de risco | Tipos de deteção de risco associados à inscrição. Os valores possíveis são: `unlikelyTravel` , , , , , , , , , `anonymizedIPAddress` e `maliciousIPAddress` `unfamiliarFeatures` `malwareInfectedIPAddress` `suspiciousIPAddress` `leakedCredentials` `investigationsThreatIntelligence`  `generic` `unknownFutureValue` . |
| riscoLevelAggregada | riscoLevel | Nível de risco agregado. Os valores possíveis são: `none` , , , , e `low` `medium` `high` `hidden` `unknownFutureValue` . O valor `hidden` significa que o utilizador ou o insinualado não foi ativado para a Proteção de Identidade Azure AD. **Nota:** Os detalhes desta propriedade só estão disponíveis para clientes Azure AD Premium P2. Todos os outros clientes serão `hidden` devolvidos. |
| riscoLevelDuringSignIn | riscoLevel | Nível de risco durante a entrada. Os valores possíveis são: `none` , , , , e `low` `medium` `high` `hidden` `unknownFutureValue` . O valor `hidden` significa que o utilizador ou o insinualado não foi ativado para a Proteção de Identidade Azure AD. **Nota:** Os detalhes desta propriedade só estão disponíveis para clientes Azure AD Premium P2. Todos os outros clientes serão `hidden` devolvidos. |
| Estado de risco | Estado de risco | Relatórios do utilizador arriscado, de iniciar sposição ou de uma deteção de riscos. Os valores possíveis são: `none` , , , , , , , `confirmedSafe` `remediated` `dismissed` `atRisk` `confirmedCompromised` `unknownFutureValue` . |
| DurationMs |  Este valor não é mapeado, e você pode ignorar com segurança este campo. |
| CallerIpAddress | O endereço IP do cliente que fez o pedido. | 
| CorrelationId | O GUID opcional que passou pelo cliente. Este valor pode ajudar a correlacionar as operações do lado do cliente com as operações do lado do servidor, e é útil quando está a rastrear registos que abrangem os serviços. |
| Identidade | A identidade do símbolo que foi apresentado quando fez o pedido. Pode ser uma conta de utilizador, conta do sistema ou principal serviço. |
| Level | Fornece o tipo de mensagem. Para auditoria, é sempre *informativo.* |
| Localização | Fornece a localização da atividade de inscrição. |
| Propriedades | Lista todas as propriedades associadas a insusitados. Para obter mais informações, consulte [a Referência API do Gráfico da Microsoft](/graph/api/resources/signin?view=graph-rest-beta). Este esquema utiliza os mesmos nomes de atributos que o recurso de inscrição, para legibilidade.

## <a name="next-steps"></a>Passos seguintes

* [Interpretar esquema de registos de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Leia mais sobre os registos da plataforma Azure](../../azure-monitor/essentials/platform-logs-overview.md)