---
title: Compreenda o esquema da AD Azure e expressões personalizadas
description: Este artigo descreve o esquema Azure AD, os atributos que o agente de provisionamento flui, e expressões personalizadas.
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
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299334"
---
# <a name="understand-the-azure-ad-schema"></a>Compreenda o esquema da AD Azure
Um objeto no Azure Ative Directory (Azure AD), como qualquer diretório, é uma construção programática de dados de alto nível que representa coisas como utilizadores, grupos e contactos. Quando cria um novo utilizador ou contacta em Azure AD, está a criar uma nova instância desse objeto. Estes casos podem ser diferenciados com base nas suas propriedades.

As propriedades em Azure AD são os elementos responsáveis pelo armazenamento de informações sobre um caso de um objeto em Azure AD.

O esquema da AD Azure define as regras para as quais as propriedades podem ser utilizadas numa entrada, os tipos de valores que essas propriedades podem ter, e como os utilizadores podem interagir com esses valores. 

A Azure AD tem dois tipos de propriedades:
- **Propriedades incorporadas**: Propriedades predefinidas pelo esquema Azure AD. Estas propriedades fornecem diferentes usos e podem ou não estar acessíveis.
- **Extensões**de diretório : Propriedades que são fornecidas para que possa personalizar o Azure AD para seu próprio uso. Por exemplo, se estendeu o seu Diretório Ativo no local com um determinado atributo e quer fluir esse atributo, pode usar uma das propriedades personalizadas que é fornecida. 

## <a name="attributes-and-expressions"></a>Atributos e expressões
Quando um objeto como um utilizador é provisionado para a AD Azure, é criada uma nova instância do objeto de utilizador. Esta criação inclui as propriedades desse objeto, que também são conhecidos como atributos. Inicialmente, o objeto recém-criado tem os seus atributos definidos para valores que são determinados pelas regras de sincronização. Estes atributos são então mantidos atualizados através do agente de fornecimento de nuvens.

![Fornecimento de objetos](media/concept-attributes/attribute1.png)

Por exemplo, um utilizador pode fazer parte de um departamento de Marketing. O seu atributo do departamento de Anúncios Azure é inicialmente criado quando são provisionados, e o valor é definido para Marketing. Seis meses depois, se mudarem para vendas, o seu atributo do Departamento de Diretório Ativo no local é alterado para Vendas. Esta alteração sincroniza-se com a AD Azure e reflete-se no seu objeto de utilizador Azure AD.

A sincronização do atributo pode ser direta, onde o valor em Azure AD está diretamente definido para o valor do atributo no local. Ou, uma expressão programática pode lidar com a sincronização. Uma expressão programática é necessária nos casos em que alguma lógica ou determinação deve ser feita para povoar o valor.

Por exemplo, se tivesseo atributo do correiojohn.smith@contoso.com"@contoso.come precisasse de despir a porção " e fluir apenas o valor "john.smith", usaria algo assim:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Entrada/saída da amostra:** <br>

* **INPUT** (correio):john.smith@contoso.com" "
* **SAÍDA**: "john.smith"

Para obter mais informações sobre como escrever expressões personalizadas e a sintaxe, consulte [expressões escritas para mapeamento de atributos no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

A tabela seguinte lista atributos comuns e como são sincronizados com a AD Azure.


|Active Directory no local|Tipo de mapeamento|Azure AD|
|-----|-----|-----|
|cn|Direct|nome comum
|paísCódigo|Direct|paísCódigo|
|displayName|Direct|displayName|
|nomeDado|Expressão|nomeDado|
|objectGUID|Direct|fonteAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress|

## <a name="view-the-schema"></a>Ver o esquema
> [!WARNING]
> A configuração de fornecimento de nuvem cria um diretor de serviço. O diretor de serviço é visível no portal Azure. Não deve modificar os mapeamentos de atributoutilizando a experiência principal do serviço no portal Azure.  Esta ação não é suportada.

Para ver o esquema e verificar, siga estes passos.

1.  Vá ao [Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer)
1.  Inscreva-se na sua conta de administrador global.
1.  À esquerda, selecione **modificar permissões** e certifique-se de que **o Diretório.ReadWrite.All** is *Consented*.
1.  Execute a `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`consulta. Esta consulta devolve uma lista filtrada de diretores de serviço.
1.  Localize `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` e note `"id"`o valor para .
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
1. Substitua `{Service Principal id}` pelo seu valor e `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`execute a consulta.
1. Localize `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` e note `"id"`o valor para .
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
1. Agora executa `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`a consulta.
 
    Exemplo: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   `{Service Principal Id}` Substitua `{AD2ADD Provisioning Id}` e com os seus valores.

1. Esta consulta devolve o esquema.

   ![Esquema devolvido](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Passos seguintes

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
