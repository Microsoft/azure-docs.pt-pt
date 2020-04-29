---
title: Autenticação de saída
description: Saiba como configurar ou remover a autenticação de saída para o Programador Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878974"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Autenticação de saída para O Scheduler Azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por [favor, emigre para as Aplicações Lógicas Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rapidamente possível. 
>
> O agendador já não está disponível no portal Azure, mas os cmdlets Rest [API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e recolhas de emprego.

Os trabalhos do Azure Scheduler poderão ter de chamar serviços que exijam a autenticação, como outros serviços Azure, Salesforce.com, Facebook e sites personalizados seguros. O chamado serviço pode determinar se o trabalho do Scheduler pode aceder aos recursos solicitados. 

O agendador suporta estes modelos de autenticação: 

* Autenticação do certificado de *cliente* ao utilizar certificados de cliente SSL/TLS
* Autenticação *básica*
* *Autenticação Ativa OAuth OAuth*

## <a name="add-or-remove-authentication"></a>Adicionar ou remover a autenticação

* Para adicionar a autenticação a um trabalho de Scheduler, `authentication` quando criar ou atualizar o trabalho, `request` adicione o elemento infantil JavaScript Object Notation (JSON) ao elemento. 

  As respostas nunca devolvem segredos que são passados ao serviço `authentication` Scheduler através de um pedido DE PUT, PATCH ou POST no objeto. 
  As respostas estabelecem informações secretas para nula ou podem usar um símbolo público que representa a entidade autenticada. 

* Para remover a autenticação de um trabalho de Scheduler, execute explicitamente um pedido DE PUT ou PATCH no trabalho e coloque o `authentication` objeto em nulo. A resposta não conterá propriedades de autenticação.

## <a name="client-certificate"></a>Certificado de cliente

### <a name="request-body---client-certificate"></a>Órgão de pedido - Certificado de cliente

Ao adicionar a `ClientCertificate` autenticação utilizando o modelo, especifique estes elementos adicionais no organismo de pedido.  

| Elemento | Necessário | Descrição |
|---------|----------|-------------|
| **autenticação** (elemento-mãe) | O objeto de autenticação para a utilização de um certificado de cliente SSL/TLS |
| **tipo** | Sim | O tipo de autenticação. Para os certificados de cliente SSL/TLS, o valor é `ClientCertificate`. |
| **pfx** | Sim | O conteúdo codificado base64 do ficheiro PFX |
| **palavra-passe** | Sim | A palavra-passe para aceder ao ficheiro PFX |
||| 

### <a name="response-body---client-certificate"></a>Corpo de resposta - Certificado de cliente 

Quando um pedido é enviado com informações de autenticação, a resposta contém estes elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------| 
| **autenticação** (elemento-mãe) | O objeto de autenticação para a utilização de um certificado de cliente SSL/TLS |
| **tipo** | O tipo de autenticação. Para os certificados de cliente SSL/TLS, o valor é `ClientCertificate`. |
| **certificateThumbprint** |A impressão digital do certificado |
| **certificadoNome subjectname** |O nome distinto do sujeito do certificado |
| **certificadoExpiração** | Data de validade do certificado |
||| 

### <a name="sample-rest-request---client-certificate"></a>Pedido de recção de amostra - Certificado de cliente

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

### <a name="sample-rest-response---client-certificate"></a>Resposta do REST da amostra - Certificado de cliente

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

## <a name="basic"></a>Básico

### <a name="request-body---basic"></a>Órgão de pedido - Básico

Ao adicionar a `Basic` autenticação utilizando o modelo, especifique estes elementos adicionais no organismo de pedido.

| Elemento | Necessário | Descrição |
|---------|----------|-------------|
| **autenticação** (elemento-mãe) | O objeto de autenticação para utilização de autenticação básica | 
| **tipo** | Sim | O tipo de autenticação. Para autenticação básica, `Basic`o valor é . | 
| **nome de utilizador** | Sim | O nome de utilizador para autenticar | 
| **palavra-passe** | Sim | A palavra-passe para autenticar |
|||| 

### <a name="response-body---basic"></a>Corpo de resposta - Básico

Quando um pedido é enviado com informações de autenticação, a resposta contém estes elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------|
| **autenticação** (elemento-mãe) | O objeto de autenticação para utilização de autenticação básica |
| **tipo** | O tipo de autenticação. Para autenticação básica, `Basic`o valor é . |
| **nome de utilizador** | O nome de utilizador autenticado |
||| 

### <a name="sample-rest-request---basic"></a>Pedido de REPOUSO da amostra - Básico

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

### <a name="sample-rest-response---basic"></a>Resposta do REST da amostra - Básico

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

## <a name="active-directory-oauth"></a>Diretório Ativo OAuth

### <a name="request-body---active-directory-oauth"></a>Órgão de pedido - Diretório Ativo OAuth 

Ao adicionar a `ActiveDirectoryOAuth` autenticação utilizando o modelo, especifique estes elementos adicionais no organismo de pedido.

| Elemento | Necessário | Descrição |
|---------|----------|-------------|
| **autenticação** (elemento-mãe) | Sim | O objeto de autenticação para a utilização da autenticação ActiveDirectoryOAuth |
| **tipo** | Sim | O tipo de autenticação. Para autenticação ActiveDirectoryOAuth, `ActiveDirectoryOAuth`o valor é . |
| **inquilino** | Sim | O inquilino identificador para o inquilino da AD Azure. Para encontrar o identificador de inquilino para `Get-AzureAccount` o inquilino da AD Azure, corra em Azure PowerShell. |
| **público** | Sim | Este valor está `https://management.core.windows.net/`definido para . | 
| **clientId** | Sim | O identificador de cliente para a aplicação Azure AD | 
| **segredo** | Sim | O segredo para o cliente que está a pedir o símbolo | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Corpo de resposta - Diretório Ativo OAuth

Quando um pedido é enviado com informações de autenticação, a resposta contém estes elementos de autenticação.

| Elemento | Descrição |
|---------|-------------|
| **autenticação** (elemento-mãe) | O objeto de autenticação para a utilização da autenticação ActiveDirectoryOAuth |
| **tipo** | O tipo de autenticação. Para autenticação ActiveDirectoryOAuth, `ActiveDirectoryOAuth`o valor é . | 
| **inquilino** | O inquilino identificador para o inquilino da AD Azure |
| **público** | Este valor está `https://management.core.windows.net/`definido para . |
| **clientId** | O identificador de cliente para a aplicação Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Pedido de REPOUSO da amostra - Diretório Ativo OAuth

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

### <a name="sample-rest-response---active-directory-oauth"></a>Resposta do REST da amostra - Diretório Ativo OAuth

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

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)