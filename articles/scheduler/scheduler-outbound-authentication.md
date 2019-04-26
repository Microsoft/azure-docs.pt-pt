---
title: Autenticação de saída - Azure Scheduler
description: Saiba como configurar ou remover a autenticação de saída do agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 42d6ec93a3382f494b49fb574c4aee5e8eec142a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344353"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Autenticação de saída do agendador do Azure

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Tarefas do Scheduler do Azure poderão ter de chamar serviços que necessitem de autenticação, como outros serviços do Azure, Salesforce.com, Facebook e Web sites personalizados seguros. O serviço chamado pode determinar se a tarefa do Scheduler pode acessar os recursos de pedido. 

O Scheduler suporta estes modelos de autenticação: 

* *Certificado de cliente* autenticação ao utilizar certificados de cliente SSL/TLS
* *Básico* autenticação
* *Active Directory OAuth* autenticação

## <a name="add-or-remove-authentication"></a>Adicionar ou remover a autenticação

* Para adicionar autenticação para uma tarefa do agendador, quando criar ou atualizar a tarefa, adicione a `authentication` elemento subordinado de JavaScript Object Notation (JSON) para o `request` elemento. 

  As respostas nunca retornam segredos que são transmitidos para o serviço de agendador através de um pedido PUT, PATCH ou POST no `authentication` objeto. 
  As respostas definir informações secretas como nula ou poderão utilizar um token de público que representa a entidade autenticada. 

* Para remover a autenticação de uma tarefa do agendador, explicitamente executar um pedido PUT ou PATCH no trabalho e defina o `authentication` objeto como null. A resposta não contém as propriedades de autenticação.

## <a name="client-certificate"></a>Certificado de cliente

### <a name="request-body---client-certificate"></a>Corpo do pedido - certificado de cliente

Ao adicionar a autenticação com o `ClientCertificate` modelar, especifique esses elementos adicionais no corpo do pedido.  

| Elemento | Necessário | Descrição |
|---------|----------|-------------|
| **autenticação** (elemento principal) | O objeto de autenticação para utilizar um certificado de cliente SSL |
| **tipo** | Sim | O tipo de autenticação. Para certificados de cliente SSL, o valor é `ClientCertificate`. |
| **pfx** | Sim | O conteúdo do ficheiro PFX codificada em base64 |
| **password** | Sim | A palavra-passe para aceder ao ficheiro PFX |
||| 

### <a name="response-body---client-certificate"></a>Corpo de resposta - certificado de cliente 

Quando é enviado um pedido com informações de autenticação, a resposta contém esses elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------| 
| **autenticação** (elemento principal) | O objeto de autenticação para utilizar um certificado de cliente SSL |
| **tipo** | O tipo de autenticação. Para certificados de cliente SSL, o valor é `ClientCertificate`. |
| **certificateThumbprint** |O thumbprint do certificado |
| **certificateSubjectName** |O nome distinto do requerente de certificado |
| **certificateExpiration** | Data de expiração do certificado |
||| 

### <a name="sample-rest-request---client-certificate"></a>Pedido de REST de exemplo - certificado de cliente

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

### <a name="sample-rest-response---client-certificate"></a>Resposta de REST de exemplo - certificado de cliente

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

### <a name="request-body---basic"></a>Corpo - Basic do pedido

Ao adicionar a autenticação com o `Basic` modelar, especifique esses elementos adicionais no corpo do pedido.

| Elemento | Necessário | Descrição |
|---------|----------|-------------|
| **autenticação** (elemento principal) | O objeto de autenticação para utilizar a autenticação básica | 
| **tipo** | Sim | O tipo de autenticação. Para a autenticação básica, o valor é `Basic`. | 
| **username** | Sim | O nome de utilizador para autenticar | 
| **password** | Sim | A palavra-passe para autenticar |
|||| 

### <a name="response-body---basic"></a>Corpo de resposta - básico

Quando é enviado um pedido com informações de autenticação, a resposta contém esses elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------|
| **autenticação** (elemento principal) | O objeto de autenticação para utilizar a autenticação básica |
| **tipo** | O tipo de autenticação. Para a autenticação básica, o valor é `Basic`. |
| **username** | O nome de utilizador autenticado |
||| 

### <a name="sample-rest-request---basic"></a>Pedido de REST de exemplo - básico

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

### <a name="sample-rest-response---basic"></a>Resposta de REST de exemplo - básico

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

### <a name="request-body---active-directory-oauth"></a>Corpo - Active Directory OAuth do pedido 

Ao adicionar a autenticação com o `ActiveDirectoryOAuth` modelar, especifique esses elementos adicionais no corpo do pedido.

| Elemento | Necessário | Descrição |
|---------|----------|-------------|
| **autenticação** (elemento principal) | Sim | O objeto de autenticação para utilizar a autenticação de ActiveDirectoryOAuth |
| **tipo** | Sim | O tipo de autenticação. Para a autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. |
| **tenant** | Sim | O identificador do inquilino para o inquilino do Azure AD. Para localizar o identificador do inquilino para o inquilino do Azure AD, execute `Get-AzureAccount` no Azure PowerShell. |
| **Público-alvo** | Sim | Este valor é definido como `https://management.core.windows.net/`. | 
| **clientId** | Sim | O identificador de cliente para a aplicação do Azure AD | 
| **secret** | Sim | O segredo do cliente que está a pedir o token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Corpo de resposta - OAuth do Active Directory

Quando é enviado um pedido com informações de autenticação, a resposta contém esses elementos de autenticação.

| Elemento | Descrição |
|---------|-------------|
| **autenticação** (elemento principal) | O objeto de autenticação para utilizar a autenticação de ActiveDirectoryOAuth |
| **tipo** | O tipo de autenticação. Para a autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. | 
| **tenant** | O identificador do inquilino para o inquilino do Azure AD |
| **Público-alvo** | Este valor é definido como `https://management.core.windows.net/`. |
| **clientId** | O identificador de cliente para a aplicação do Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Pedido de REST de exemplo - OAuth do Active Directory

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

### <a name="sample-rest-response---active-directory-oauth"></a>Resposta de REST de exemplo - OAuth do Active Directory

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
* [API REST do agendador do Azure](https://msdn.microsoft.com/library/mt629143)
* [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)
