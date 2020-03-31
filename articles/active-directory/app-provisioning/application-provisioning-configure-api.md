---
title: Utilize APIs do Microsoft Graph para configurar o provisionamento - Diretório Ativo Azure / Microsoft Docs
description: Precisa de configurar o fornecimento para várias instâncias de um pedido? Aprenda a economizar tempo utilizando as APIs do Microsoft Graph para automatizar a configuração do fornecimento automático.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72217a565071f9531281af1862ba3681e353a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481471"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Configure o fornecimento usando APIs do Microsoft Graph

O portal Azure é uma forma conveniente de configurar o provisionamento para aplicações individuais uma de cada vez. Mas se estiver a criar várias ou mesmo centenas de casos de uma aplicação, pode ser mais fácil automatizar a criação e configuração de apps com as APIs do Microsoft Graph. Este artigo descreve como automatizar a configuração de fornecimento através de APIs. Este método é comumente usado para aplicações como [amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Visão geral dos passos para a utilização de APIs do Microsoft Graph para automatizar a configuração de provisionamento**


|Passo  |Detalhes  |
|---------|---------|
|[Passo 1. Criar a aplicação da galeria](#step-1-create-the-gallery-application)     |Inscreva-se no cliente da API <br> Recuperar o modelo de aplicação da galeria <br> Criar a aplicação da galeria         |
|[Passo 2. Criar trabalho de provisionamento com base no modelo](#step-2-create-the-provisioning-job-based-on-the-template)     |Recuperar o modelo para o conector de provisionamento <br> Criar o emprego de provisionamento         |
|[Passo 3. Autorizar acesso](#step-3-authorize-access)     |Testar a ligação à aplicação <br> Guarde as credenciais         |
|[Passo 4. Começar a provisionar o emprego](#step-4-start-the-provisioning-job)     |Iniciar a tarefa         |
|[Passo 5. Monitorização do provisionamento](#step-5-monitor-provisioning)     |Verifique o estado do trabalho de provisionamento <br> Recupere os registos de fornecimento         |

> [!NOTE]
> Os objetos de resposta apresentados neste artigo podem ser encurtados para a legibilidade. Todas as propriedades serão devolvidas de uma chamada real.

## <a name="step-1-create-the-gallery-application"></a>Passo 1: Criar a aplicação da galeria

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Inscreva-se no Microsoft Graph Explorer (recomendado), Carteiro ou qualquer outro cliente da API que utilize

1. Inicie [o Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Selecione o botão "Iniciar sessão com a Microsoft" e iniciar sessão utilizando o administrador global da AD Azure ou credenciais de administrador de aplicações.

    ![Sign-in de gráfico](./media/application-provisioning-configure-api/wd_export_02.png)

1. Após um início de sessão bem sucedido, verá os detalhes da conta do utilizador no painel da mão esquerda.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Recupere o identificador de modelo de aplicação da galeria
As aplicações na galeria de aplicações da AD Azure têm um modelo de [aplicação](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) que descreve os metadados para essa aplicação. Utilizando este modelo, pode criar uma instância do diretor de candidatura e serviço no seu inquilino para gestão.

#### <a name="request"></a>*Pedido*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Resposta*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Criar a aplicação da galeria

Utilize o ID do modelo recuperado para a sua aplicação no último passo para [criar uma instância](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) do diretor de aplicação e serviço no seu inquilino.

#### <a name="request"></a>*Pedido*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Resposta*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Passo 2: Criar o trabalho de provisionamento com base no modelo

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Recuperar o modelo para o conector de provisionamento

As aplicações na galeria que estão habilitadas para o provisionamento têm modelos para simplificar a configuração. Utilize o pedido abaixo para [recuperar o modelo para a configuração de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http). Note que terá de fornecer a identificação. O ID refere-se ao recurso anterior, que neste caso é o Diretor de Serviço. 

#### <a name="request"></a>*Pedido*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Resposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Criar o emprego de provisionamento
Para permitir o provisionamento, primeiro terá de [criar um emprego.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http) Utilize o pedido abaixo para criar um trabalho de provisionamento. Utilize o modeloId do passo anterior ao especificar o modelo a utilizar para o trabalho.

#### <a name="request"></a>*Pedido*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Resposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Passo 3: Autorizar o acesso

### <a name="test-the-connection-to-the-application"></a>Testar a ligação à aplicação

Teste a ligação com a aplicação de terceiros. O exemplo abaixo é para uma aplicação que requer clientSecret e secretToken. Cada aplicação tem os seus requisitos. As aplicações usam frequentemente o BaseAddress no lugar do ClientSecret. Para determinar quais as credenciais que a sua aplicação necessita, navegue para a página de configuração de provisionamento para a sua aplicação e no modo desenvolvedor clique na ligação de teste. O tráfego da rede mostrará os parâmetros utilizados para credenciais. A lista completa de credenciais pode ser consultada [aqui.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) 

#### <a name="request"></a>*Pedido*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Resposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Guarde as suas credenciais

A configuração do provisionamento requer estabelecer um fundo entre a Azure AD e a aplicação. Autorize o acesso à aplicação de terceiros. O exemplo abaixo é para uma aplicação que requer clientSecret e secretToken. Cada aplicação tem os seus requisitos. Reveja a documentação da [API](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) para ver as opções disponíveis. 

#### <a name="request"></a>*Pedido*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Resposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Passo 4: Iniciar o trabalho de provisionamento
Agora que o trabalho de provisionamento está configurado, utilize o seguinte comando para [iniciar o trabalho](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 


#### <a name="request"></a>*Pedido*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Resposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Passo 5: Fornecimento de monitores

### <a name="monitor-the-provisioning-job-status"></a>Monitorizar o estatuto de emprego de provisionamento

Agora que o trabalho de provisionamento está em execução, utilize o seguinte comando para acompanhar o progresso do atual ciclo de provisionamento, bem como estatísticas até à data, como o número de utilizadores e grupos que foram criados no sistema-alvo. 

#### <a name="request"></a>*Pedido*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Resposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Monitorizar os eventos de provisionamento utilizando os registos de provisionamento
Além de monitorizar o estado do trabalho de provisionamento, pode utilizar os [registos](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) de fornecimento para consultar todos os eventos que ocorrem (por exemplo, consulta para um determinado utilizador e determinar se foram aprovisionados com sucesso).

#### <a name="request"></a>*Pedido*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Resposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Artigos relacionados

- [Reveja a documentação de sincronização do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrando uma aplicação SCIM personalizada com AD Azure](use-scim-to-provision-users-and-groups.md)
