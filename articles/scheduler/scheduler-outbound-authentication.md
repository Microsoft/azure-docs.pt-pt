---
title: Autenticação de saída-Agendador do Azure
description: Saiba como configurar ou remover a autenticação de saída para o Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 2ea09330fb8d3d97da5fbc197dba9668f1a4f685
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300854"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Autenticação de saída para o Agendador do Azure

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível.

Os trabalhos do Agendador do Azure podem precisar chamar serviços que exigem autenticação, como outros serviços do Azure, Salesforce.com, Facebook e sites seguros personalizados. O serviço chamado pode determinar se o trabalho do Agendador pode acessar os recursos solicitados. 

O Agendador dá suporte a estes modelos de autenticação: 

* Autenticação de *certificado de cliente* ao usar certificados de cliente SSL/TLS
* Autenticação *básica*
* *Active Directory Autenticação OAuth*

## <a name="add-or-remove-authentication"></a>Adicionar ou remover autenticação

* Para adicionar autenticação a um trabalho do Agendador, ao criar ou atualizar o trabalho, adicione o `authentication` elemento filho JavaScript Object Notation (JSON) `request` ao elemento. 

  As respostas nunca retornam segredos que são passados para o serviço do Agendador por meio de uma solicitação Put, patch `authentication` ou post no objeto. 
  As respostas definem informações secretas como nulas ou podem usar um token público que represente a entidade autenticada. 

* Para remover a autenticação de um trabalho do Agendador, execute explicitamente uma solicitação Put ou patch no trabalho e defina o `authentication` objeto como NULL. A resposta não conterá nenhuma propriedade de autenticação.

## <a name="client-certificate"></a>Certificado do cliente

### <a name="request-body---client-certificate"></a>Corpo da solicitação-certificado do cliente

Ao adicionar a autenticação usando `ClientCertificate` o modelo, especifique esses elementos adicionais no corpo da solicitação.  

| Elemento | Requerido | Descrição |
|---------|----------|-------------|
| **autenticação** do (elemento pai) | O objeto de autenticação para usar um certificado de cliente SSL |
| **type** | Sim | O tipo de autenticação. Para certificados de cliente SSL, o valor `ClientCertificate`é. |
| **pfx** | Sim | O conteúdo codificado na base64 do arquivo PFX |
| **password** | Sim | A senha para acessar o arquivo PFX |
||| 

### <a name="response-body---client-certificate"></a>Corpo da resposta-certificado do cliente 

Quando uma solicitação é enviada com informações de autenticação, a resposta contém esses elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------| 
| **autenticação** do (elemento pai) | O objeto de autenticação para usar um certificado de cliente SSL |
| **type** | O tipo de autenticação. Para certificados de cliente SSL, o valor `ClientCertificate`é. |
| **certificateThumbprint** |A impressão digital do certificado |
| **certificateSubjectName** |O nome distinto da entidade do certificado |
| **certificateExpiration** | A data de validade do certificado |
||| 

### <a name="sample-rest-request---client-certificate"></a>Solicitação REST de amostra-certificado do cliente

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Exemplo de resposta REST-certificado do cliente

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Básica

### <a name="request-body---basic"></a>Corpo da solicitação-básico

Ao adicionar a autenticação usando `Basic` o modelo, especifique esses elementos adicionais no corpo da solicitação.

| Elemento | Requerido | Descrição |
|---------|----------|-------------|
| **autenticação** do (elemento pai) | O objeto de autenticação para usar a autenticação básica | 
| **type** | Sim | O tipo de autenticação. Para a autenticação básica, o valor `Basic`é. | 
| **username** | Sim | O nome de usuário a ser autenticado | 
| **password** | Sim | A senha a ser autenticada |
|||| 

### <a name="response-body---basic"></a>Corpo da resposta-básico

Quando uma solicitação é enviada com informações de autenticação, a resposta contém esses elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------|
| **autenticação** do (elemento pai) | O objeto de autenticação para usar a autenticação básica |
| **type** | O tipo de autenticação. Para a autenticação básica, o valor `Basic`é. |
| **username** | O nome de usuário autenticado |
||| 

### <a name="sample-rest-request---basic"></a>Exemplo de solicitação REST – básico

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Exemplo de resposta REST-básico

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Corpo da solicitação-Active Directory OAuth 

Ao adicionar a autenticação usando `ActiveDirectoryOAuth` o modelo, especifique esses elementos adicionais no corpo da solicitação.

| Elemento | Requerido | Descrição |
|---------|----------|-------------|
| **autenticação** do (elemento pai) | Sim | O objeto de autenticação para usar a autenticação ActiveDirectoryOAuth |
| **type** | Sim | O tipo de autenticação. Para a autenticação ActiveDirectoryOAuth, o valor `ActiveDirectoryOAuth`é. |
| **tenant** | Sim | O identificador do locatário para o locatário do Azure AD. Para localizar o identificador de locatário para o locatário do Azure ad `Get-AzureAccount` , execute em Azure PowerShell. |
| **platéia** | Sim | Esse valor é definido como `https://management.core.windows.net/`. | 
| **clientId** | Sim | O identificador do cliente para o aplicativo do Azure AD | 
| **secret** | Sim | O segredo para o cliente que está solicitando o token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Corpo da resposta-Active Directory OAuth

Quando uma solicitação é enviada com informações de autenticação, a resposta contém esses elementos de autenticação.

| Elemento | Descrição |
|---------|-------------|
| **autenticação** do (elemento pai) | O objeto de autenticação para usar a autenticação ActiveDirectoryOAuth |
| **type** | O tipo de autenticação. Para a autenticação ActiveDirectoryOAuth, o valor `ActiveDirectoryOAuth`é. | 
| **tenant** | O identificador do locatário para o locatário do Azure AD |
| **platéia** | Esse valor é definido como `https://management.core.windows.net/`. |
| **clientId** | O identificador do cliente para o aplicativo do Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Solicitação REST de amostra-Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Resposta REST de exemplo-Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)
* [API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)
* [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)
