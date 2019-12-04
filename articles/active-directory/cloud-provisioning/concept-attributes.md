---
title: Noções básicas sobre o esquema e as expressões personalizadas do Azure AD
description: Este tópico descreve o esquema do Azure AD, os atributos que o agente de provisionamento flui e expressões personalizadas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794500"
---
# <a name="understanding-the-azure-ad-schema"></a>Noções básicas sobre o esquema do AD do Azure
Um objeto no Azure AD, como qualquer diretório, é uma construção de dados de alto nível programática que representa itens como usuários, grupos e contatos.  Ao criar um novo usuário ou contato no Azure AD, você está criando uma nova instância desse objeto.  Essas instâncias podem ser diferenciadas com base em suas propriedades.

As propriedades, no Azure AD são os elementos responsáveis por armazenar informações sobre uma instância de um objeto no Azure AD.  

O esquema do AD do Azure define as regras para as quais as propriedades podem ser usadas em uma entrada, os tipos de valores que essas propriedades podem ter e como os usuários podem interagir com esses valores. 

O Azure AD tem dois tipos de propriedades.  As propriedades são:
- **Propriedades internas** – propriedades que são predefinidas pelo esquema do Azure AD.  Essas propriedades fornecem diferentes usos e podem ou não estar acessíveis.
- **Extensões de diretório** – propriedades que são fornecidas para que você possa personalizar o Azure ad para seu próprio uso.  Por exemplo, se você estendeu sua Active Directory local com um determinado atributo e deseja fluir para esse atributo, você pode usar uma das propriedades personalizadas que são fornecidas. 

## <a name="attributes-and-expressions"></a>Atributos e expressões
Quando um objeto, como um usuário, é provisionado no Azure AD, uma nova instância do objeto de usuário é criada.  Essa criação inclui as propriedades desse objeto, que também são conhecidas como atributos.  Inicialmente, o objeto recém-criado terá seus atributos definidos como valores que são determinados pelas regras de sincronização.  Esses atributos são então mantidos atualizados por meio do agente de provisionamento de nuvem.

![](media/concept-attributes/attribute1.png)

Por exemplo, se um usuário fizer parte do departamento de marketing, o atributo de departamento do Azure AD será criado inicialmente quando for provisionado e o valor será definido como marketing.  Mas, depois, seis meses depois, eles mudam para vendas.  Seu atributo de departamento do AD local é alterado para vendas.  Essa alteração, em seguida, será sincronizada com o Azure AD e será refletida no objeto de usuário do Azure AD.

A sincronização de atributo pode ser direta, em que o valor no Azure AD é diretamente definido como o valor do atributo local.  Ou pode haver uma expressão programática que manipula essa sincronização.  Uma expressão programática seria necessária nos casos em que alguma lógica ou uma determinação precisava ser feita para popular o valor.

Por exemplo, se eu tivesse meu atributo mail ("john.smith@contoso.com") e precisava remover a parte "@contoso.com" e fluir apenas o valor "John. Smith", usaria algo assim:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Entrada/saída de exemplo:** <br>

* **Entrada** (email): "john.smith@contoso.com"
* **Saída**: "John. Smith"

Para obter informações adicionais, sobre como escrever expressões personalizadas e a sintaxe, consulte [escrevendo expressões para mapeamentos de atributo em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

A lista a seguir são atributos comuns e como eles são sincronizados com o Azure AD.


|Active Directory no local|Tipo de mapeamento|Azure AD|
|-----|-----|-----|
|Hong|Direto|CommonName
|countryCode|Direto|countryCode|
|displayName|Direto|displayName|
|givenName|Expressão|givenName|
|objectGUID|Direto|sourceAnchorBinary|  
|userprincipalName|Direto|userPrincipalName|
|ProxyAdress|Direto|ProxyAddress|

## <a name="viewing-the-schema"></a>Exibindo o esquema
Para exibir o esquema e verificá-lo, execute as seguintes etapas:

1.  Navegue até o [Gerenciador de gráficos](https://developer.microsoft.com/graph/graph-explorer).
2.  Entre com sua conta de administrador global
3.  À esquerda, clique em **Modificar permissões** e verifique se **Directory. ReadWrite. All** está consentido.
4.  Execute a seguinte consulta: https://graph.microsoft.com/beta/serviceprincipals/.  Essa consulta retornará uma lista de entidades de serviço.
5.  Localize "appDisplayName": "Active Directory para Azure Active Directory provisionamento" e observe o valor de "ID:".
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
6. Substitua a {ID da entidade de serviço} pelo valor e execute a seguinte consulta: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. Localize a seção "ID": "AD2AADProvisioning. fd1c9b9e8077402c8bc03a7186c8f976" e anote o "ID:".
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
8. Agora, execute a seguinte consulta: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`
 
    Exemplo: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 Substitua {ID da entidade de serviço} e {AD2ADD ID de provisionamento} pelos seus valores.

9. Essa consulta retornará o esquema.
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
