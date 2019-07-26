---
title: Tratamento de exceção & cenário de log de erros-aplicativos lógicos do Azure | Microsoft Docs
description: Aqui está um caso de uso real sobre o tratamento de exceção avançada e o log de erros nos aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: hedidin
ms.author: estfan
ms.reviewer: LADocs
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: ec01f738ee4943659de1b49ab8d52218e6a8fb79
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385457"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Cenário: Tratamento de exceção e log de erros para aplicativos lógicos

Este cenário descreve como você pode estender um aplicativo lógico para dar melhor suporte ao tratamento de exceção. Usamos um caso de uso da vida real para responder à pergunta: "Os aplicativos lógicos do Azure dão suporte ao tratamento de exceções e erros?"

> [!NOTE]
> O esquema atual de aplicativos lógicos do Azure fornece um modelo padrão para respostas de ação. Esse modelo inclui validação interna e respostas de erro retornadas de um aplicativo de API.

## <a name="scenario-and-use-case-overview"></a>Visão geral do cenário e do caso de uso

Esta é a história como o caso de uso para este cenário: 

Uma organização de saúde conhecida nos contratou para desenvolver uma solução do Azure que criaria um portal de pacientes usando o Microsoft Dynamics CRM Online. Eles precisavam enviar registros de compromissos entre o portal do paciente do Dynamics CRM Online e o Salesforce. Fomos solicitados a usar o padrão [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) para todos os registros de pacientes.

O projeto tinha dois requisitos principais:  

* Um método para registrar em log os registros enviados do portal do Dynamics CRM Online
* Uma maneira de exibir os erros que ocorreram no fluxo de trabalho

> [!TIP]
> Para obter um vídeo de alto nível sobre esse projeto, consulte integração [grupo]de usuários(http://www.integrationusergroup.com/logic-apps-support-error-handling/ "integração")grupo de usuários.

## <a name="how-we-solved-the-problem"></a>Como resolvemos o problema

Escolhemos [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") como um repositório para os registros de log e de erro (Cosmos DB se refere a registros como documentos). Como os aplicativos lógicos do Azure têm um modelo padrão para todas as respostas, não precisamos criar um esquema personalizado. Poderíamos criar um aplicativo de API para **Inserir** e **consultar** os registros de erro e log. Também poderíamos definir um esquema para cada um dentro do aplicativo de API.  

Outro requisito era limpar os registros após uma determinada data. Cosmos DB tem uma [propriedade chamada TTL](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "(vida") útil), que nos permitiu definir um valor de **vida** útil para cada registro ou coleção. Esse recurso eliminou a necessidade de excluir manualmente os registros em Cosmos DB.

> [!IMPORTANT]
> Para concluir este tutorial, você precisa criar um banco de dados Cosmos DB e duas coleções (log e erros).

## <a name="create-the-logic-app"></a>Criar o aplicativo lógico

A primeira etapa é criar o aplicativo lógico e abrir o aplicativo no designer de aplicativo lógico. Neste exemplo, estamos usando aplicativos lógicos pai-filho. Vamos supor que já criamos o pai e vamos criar um aplicativo lógico filho.

Como vamos registrar o registro que sai do Dynamics CRM Online, vamos começar na parte superior. Devemos usar um gatilho de **solicitação** porque o aplicativo lógico pai dispara esse filho.

### <a name="logic-app-trigger"></a>Acionador da aplicação lógica

Estamos usando um gatilho de **solicitação** , conforme mostrado no exemplo a seguir:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Passos

Devemos registrar a origem (solicitação) do registro de pacientes no portal do Dynamics CRM Online.

1. É necessário obter um novo registro de compromisso do Dynamics CRM Online.

   O gatilho proveniente do CRM nos fornece a Patenteid de **CRM**, **o tipo de registro**, o **registro novo ou atualizado** (valor booliano novo ou de atualização) e **SalesforceId**. O **SalesforceId** pode ser nulo porque é usado apenas para uma atualização.
   Obtemos o registro do CRM usando o CRM **pacienteid** e o **tipo de registro**.

2. Em seguida, precisamos adicionar nossa Azure Cosmos DB operação **InsertLogEntry** do aplicativo de API do SQL, como mostrado aqui no designer de aplicativo lógico.

   **Inserir entrada de log**

   ![Inserir entrada de log](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Inserir entrada de erro**

   ![Inserir entrada de log](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Verificar se há falha de criação de registro**

   ![Condição](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Código-fonte do aplicativo lógico

> [!NOTE]
> Os exemplos a seguir são apenas exemplos. Como este tutorial se baseia em uma implementação agora em produção, o valor de um **nó de origem** pode não exibir propriedades relacionadas ao agendamento de um compromisso. > 

### <a name="logging"></a>Registo

O exemplo de código do aplicativo lógico a seguir mostra como manipular o registro em log.

#### <a name="log-entry"></a>Entrada de log

Aqui está o código-fonte do aplicativo lógico para inserir uma entrada de log.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Solicitação de log

Aqui está a mensagem de solicitação de log postada para o aplicativo de API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Resposta de log

Aqui está a mensagem de resposta de log do aplicativo de API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Agora, vamos examinar as etapas de tratamento de erro.

### <a name="error-handling"></a>Processamento de erros

O exemplo de código do aplicativo lógico a seguir mostra como você pode implementar o tratamento de erros.

#### <a name="create-error-record"></a>Criar registro de erro

Aqui está o código-fonte do aplicativo lógico para criar um registro de erro.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Inserir erro em Cosmos DB--solicitação

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Inserir erro em Cosmos DB--resposta

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Resposta de erro do Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Retornar a resposta de volta para o aplicativo lógico pai

Depois de obter a resposta, você pode passar a resposta de volta para o aplicativo lógico pai.

#### <a name="return-success-response-to-parent-logic-app"></a>Retornar resposta de êxito ao aplicativo lógico pai

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Retornar resposta de erro para o aplicativo lógico pai

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Repositório de Cosmos DB e portal

Nossa solução acrescentou recursos com [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portal de gerenciamento de erros

Para exibir os erros, você pode criar um aplicativo Web MVC para exibir os registros de erro de Cosmos DB. As operações listar, **detalhes**, **Editar**e **excluir** estão incluídas na versão atual.

> [!NOTE]
> Operação de edição: Cosmos DB substitui todo o documento. Os registros mostrados nas exibições de **lista** e de **detalhes** são apenas exemplos. Eles não são registros de compromissos reais do paciente.

Aqui estão exemplos de nossos detalhes do aplicativo MVC criados com a abordagem descrita anteriormente.

#### <a name="error-management-list"></a>Lista de gerenciamento de erros
![Lista de Erros](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Exibição de detalhes de gerenciamento de erros
![Detalhes do Erro](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de gerenciamento de log

Para exibir os logs, também criamos um aplicativo Web MVC. Aqui estão exemplos de nossos detalhes do aplicativo MVC criados com a abordagem descrita anteriormente.

#### <a name="sample-log-detail-view"></a>Exibição de detalhes do log de exemplo
![Exibição de detalhes do log](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalhes do aplicativo de API

#### <a name="logic-apps-exception-management-api"></a>API de gerenciamento de exceções de aplicativos lógicos

Nosso aplicativo de API de gerenciamento de exceções de aplicativos lógicos do Azure de software livre fornece funcionalidade conforme descrito aqui – há dois controladores:

* **ErrorController** insere um registro de erro (documento) em uma coleção de Azure Cosmos DB.
* **LogController** Insere um registro de log (documento) em uma coleção de Azure Cosmos DB.

> [!TIP]
> Ambos os controladores `async Task<dynamic>` usam operações, permitindo que as operações sejam resolvidas em tempo de execução, para que possamos criar o esquema de Azure Cosmos DB no corpo da operação. 
> 

Cada documento no Azure Cosmos DB deve ter uma ID exclusiva. Estamos usando `PatientId` e adicionando um carimbo de data/hora que é convertido em um valor de carimbo de data/hora UNIX (duplo). Truncamos o valor para remover o valor fracionário.

Você pode exibir o código-fonte da nossa API do controlador de erros do [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Chamamos a API de um aplicativo lógico usando a seguinte sintaxe:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

A expressão no exemplo de código anterior verifica o status *Create_NewPatientRecord* de **Failed**.

## <a name="summary"></a>Resumo

* Você pode implementar facilmente o registro em log e o tratamento de erros em um aplicativo lógico.
* Você pode usar Azure Cosmos DB como o repositório para registros de log e de erro (documentos).
* Você pode usar o MVC para criar um portal para exibir registros de erro e log.

### <a name="source-code"></a>Código de origem

O código-fonte para o aplicativo de API de gerenciamento de exceções de aplicativos lógicos está disponível neste [repositório do GITHUB](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de gerenciamento de exceções de aplicativo lógico").

## <a name="next-steps"></a>Passos Seguintes

* [Exibir mais exemplos e cenários de aplicativos lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Saiba mais sobre como monitorar aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
