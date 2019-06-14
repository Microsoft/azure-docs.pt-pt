---
title: Manipulação de exceção e o cenário de log de erro - Azure Logic Apps | Documentos da Microsoft
description: Eis um caso de uso real sobre a manipulação de exceção avançada e de erro ao iniciar sessão no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: hedidin
ms.author: b-hoedid
ms.reviewer: estfan, LADocs
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 58e59e4faa135e24124f494d90437b49caa30129
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60599522"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Cenário: Registo de erros para o logic apps e manipulação de exceção

Este cenário descreve como é possível estender uma aplicação lógica para suportar melhor a manipulação de exceção. Usamos um caso de uso na vida real para responder à pergunta: "O Azure Logic Apps suporta exceção e tratamento de erros?"

> [!NOTE]
> O esquema atual do Azure Logic Apps fornece um modelo padrão para respostas de ação. Esse modelo inclui os internas de validação e as respostas de erro retornadas de uma aplicação API.

## <a name="scenario-and-use-case-overview"></a>Descrição geral de caso de cenário e a utilização

Esta é a história como o caso de utilização para este cenário: 

Uma organização de cuidados de saúde bem conhecida envolvidos-nos para desenvolver uma solução do Azure que criaria um portal do doente com o Microsoft Dynamics CRM Online. Eles necessários para enviar registos de compromissos entre o portal do doente Dynamics CRM Online e o Salesforce. Perguntamos a utilizar o [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) standard para registos de todos os pacientes.

O projeto tinha dois requisitos principais:  

* Um método para criar registos enviados a partir do portal do Dynamics CRM Online
* Uma forma de ver quaisquer erros que ocorreram no fluxo de trabalho

> [!TIP]
> Para um vídeo de alto nível sobre esse projeto, consulte [grupo de utilizadores Integration](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "grupo de utilizadores Integration").

## <a name="how-we-solved-the-problem"></a>Como solucionamos o problema

Escolhemos [do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "do Azure Cosmos DB") como um repositório para os registros de log e o erro (Cosmos DB refere-se aos registos como documentos). Como o Azure Logic Apps tem um modelo padrão para todas as respostas, não teríamos de criar um esquema personalizado. Podemos criar uma aplicação API para **inserir** e **consulta** para registos de erros e registos. Poderíamos também definir um esquema para cada dentro da aplicação de API.  

Outro requisito foi remover registos após uma determinada data. O cosmos DB tem uma propriedade chamada [TTL](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "TTL") (TTL), que nos permitiu definir um **TTL** valor para cada registo ou a coleção. Esta capacidade eliminou a necessidade de eliminar manualmente os registos no Cosmos DB.

> [!IMPORTANT]
> Para concluir este tutorial, terá de criar uma base de dados do Cosmos DB e duas coleções (Registro em log e erros).

## <a name="create-the-logic-app"></a>Criar a aplicação lógica

A primeira etapa é criar a aplicação lógica e abrir a aplicação no Estruturador da aplicação lógica. Neste exemplo, estamos a utilizar aplicações de lógica principal-subordinado. Vamos supor que já tenha criado o elemento principal e vamos criar uma aplicação de lógica de subordinados.

Uma vez, vamos iniciar o registo oriundo de Dynamics CRM Online, vamos começar na parte superior. Temos de utilizar um **pedir** acionar uma vez que a aplicação de lógica principal aciona menor.

### <a name="logic-app-trigger"></a>Acionador da aplicação lógica

Estamos a utilizar um **pedir** acionar conforme mostrado no exemplo a seguir:

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

Partir do portal do Dynamics CRM Online, tem de iniciar a origem (solicitação) de registros de pacientes.

1. Podemos tem de obter um novo registo de compromisso do Dynamics CRM Online.

   O acionador de CRM fornece-nos com o **CRM PatentId**, **tipo de registo**, **novos ou atualizados registo** (novo ou atualizar o valor booleano), e  **SalesforceId**. O **SalesforceId** pode ser nulo, porque só é utilizado para uma atualização.
   Vamos obter o registro CRM, utilizando o CRM **PatientID** e o **tipo de registo**.

2. Em seguida, precisamos de adicionar a nossa aplicação de API de SQL do Azure Cosmos DB **InsertLogEntry** operação, conforme mostrado aqui no Estruturador da aplicação lógica.

   **Inserir a entrada de registo**

   ![Inserir a entrada de registo](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Inserir a entrada de erro**

   ![Inserir a entrada de registo](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **A verificação para criar o registo falha**

   ![Condição](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Código de origem de aplicação lógica

> [!NOTE]
> Os exemplos seguintes são apenas exemplos. Uma vez que este tutorial baseia-se uma implementação em produção, o valor de um **nó de origem** podem não mostrar as propriedades que estão relacionados com o agendamento um compromisso. > 

### <a name="logging"></a>Registo

O exemplo de código de aplicação lógica seguinte mostra como lidar com o registo.

#### <a name="log-entry"></a>Entrada de registo

Aqui está o código de origem da aplicação de lógica para inserir uma entrada de registo.

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

#### <a name="log-request"></a>Pedido de registo

Segue-se a mensagem de solicitação de log postada para a aplicação API.

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


#### <a name="log-response"></a>Resposta de registo

Segue-se a mensagem de resposta de registo a partir da aplicação de API.

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

Agora vamos examinar os passos de processamento de erros.

### <a name="error-handling"></a>Processamento de erros

O exemplo de código de aplicação lógica seguinte mostra como pode implementar o tratamento de erros.

#### <a name="create-error-record"></a>Criar registo de erro

Aqui está o código de origem da aplicação de lógica para a criação de um registo de erro.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Erro de inserção para o Cosmos DB - pedido

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

#### <a name="insert-error-into-cosmos-db--response"></a>Inserir o erro no Cosmos DB - resposta

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Devolver a resposta de volta para a aplicação de lógica principal

Depois de obter a resposta, pode passar a resposta de volta para a aplicação de lógica principal.

#### <a name="return-success-response-to-parent-logic-app"></a>Devolver a resposta de êxito, a aplicação de lógica principal

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

#### <a name="return-error-response-to-parent-logic-app"></a>Devolver a resposta de erro a aplicação de lógica principal

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


## <a name="cosmos-db-repository-and-portal"></a>Repositório de cosmos DB e o portal

Nossa solução adicionado capacidades com [do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portal de gestão de erro

Para ver os erros, pode criar uma aplicação web MVC para apresentar os registos de erro do Cosmos DB. O **lista**, **detalhes**, **editar**, e **eliminar** operações estão incluídas na versão atual.

> [!NOTE]
> Operação de edição: O cosmos DB substitui o documento inteiro. Os registos que mostra o **lista** e **detalhes** exibições são apenas exemplos. Eles não forem os registros de compromisso de pacientes real.

Seguem-se exemplos de nossa detalhes da aplicação MVC criados com a abordagem descrita anteriormente.

#### <a name="error-management-list"></a>Lista de gestão de erros
![Lista de erros](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Vista de detalhes do erro gestão
![Detalhes do Erro](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de gestão de registos

Para ver os registos, também criamos uma aplicação web MVC. Seguem-se exemplos de nossa detalhes da aplicação MVC criados com a abordagem descrita anteriormente.

#### <a name="sample-log-detail-view"></a>Vista de detalhes do registo de exemplo
![Vista de detalhes do registo](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalhes da aplicação API

#### <a name="logic-apps-exception-management-api"></a>Gestão de exceção do Logic Apps API

Nossa aplicação de API de gestão de exceção do código-fonte aberto do Azure Logic Apps fornece uma funcionalidade, conforme descrito aqui – existem dois controladores:

* **ErrorController** insere um registo de erro (documento) numa coleção do Azure Cosmos DB.
* **LogController** insere um registro de log (documento) numa coleção do Azure Cosmos DB.

> [!TIP]
> Utilizam os dois controladores `async Task<dynamic>` operações, permitindo que operações resolver em tempo de execução, podemos criar o esquema do Azure Cosmos DB no corpo da operação. 
> 

Todos os documentos no Azure Cosmos DB tem de ter um ID exclusivo. Estamos a utilizar `PatientId` e adicionar um carimbo que é convertido para um valor de timestamp Unix (double). Podemos truncar o valor para remover o valor fracionário.

Pode ver o código-fonte do nosso controlador Erro API partir [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Vamos chamar a API de uma aplicação lógica, utilizando a seguinte sintaxe:

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

A expressão no exemplo de código anterior verifica para o *Create_NewPatientRecord* estado de **falha**.

## <a name="summary"></a>Resumo

* Pode facilmente implementar Registro em log e tratamento de erros numa aplicação lógica.
* Pode utilizar o Azure Cosmos DB como o repositório para registros de log e o erro (documentos).
* Pode utilizar o MVC para criar um portal para exibir registros de log e o erro.

### <a name="source-code"></a>Código de origem

O código-fonte para a gestão de exceções do Logic Apps aplicação de API está disponível neste [repositório do GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de gestão de exceção de aplicação lógica").

## <a name="next-steps"></a>Passos Seguintes

* [Ver mais exemplos de aplicação de lógica e cenários](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Saiba mais sobre a monitorização de aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Criar modelos de implementação automatizada para o logic apps](../logic-apps/logic-apps-create-deploy-template.md)
