---
title: Cenário de registo de erros de & de tratamento de exceções
description: Caso e cenário de utilização real para tratamento avançado de exceções e registo de erros em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: fdf5f25ae6f89ccc06c95ee1be021691dab0047a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322432"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Cenário: Processamento de exceções e registo de erros das aplicações lógicas

Este cenário descreve como pode estender uma aplicação lógica para melhor suportar o tratamento de exceções. Usámos um caso de uso da vida real para responder à pergunta: "A Azure Logic Apps suporta exceções e manipulação de erros?"

> [!NOTE]
> O esquema atual de Apps Lógicas Azure fornece um modelo padrão para respostas de ação. Este modelo inclui tanto a validação interna como as respostas de erro devolvidas a partir de uma aplicação API.

## <a name="scenario-and-use-case-overview"></a>Cenário e uso visão geral do caso

Aqui está a história como o caso de uso para este cenário: 

Uma organização de saúde conhecida contratou-nos para desenvolver uma solução Azure que criaria um portal de pacientes usando o Microsoft Dynamics CRM Online. Precisavam de enviar registos de consultas entre o portal de doentes Dinâmicos CRM Online e o Salesforce. Pediram-nos para usar o [padrão HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) para todos os registos dos pacientes.

O projeto tinha dois grandes requisitos:  

* Um método para registar registos enviados do portal Dynamics CRM Online
* Uma forma de ver quaisquer erros que ocorreram dentro do fluxo de trabalho

> [!TIP]
> Para obter um vídeo de alto nível sobre este projeto, consulte [o Grupo de Utilizadores de Integração.](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Grupo de Utilizadores de Integração")

## <a name="how-we-solved-the-problem"></a>Como resolvemos o problema

Escolhemos [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") Como um repositório para os registos de registos e erros (Cosmos DB refere-se a registos como documentos). Como as Azure Logic Apps têm um modelo padrão para todas as respostas, não teríamos de criar um esquema personalizado. Poderíamos criar uma aplicação API para **inserir** e **consultar** registos de erros e registos de registos. Também podemos definir um esquema para cada um dentro da app API.  

Outro requisito era expurgar os registos depois de uma certa data. Cosmos DB tem uma propriedade chamada [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Hora de Viver") (TTL), que nos permitiu definir um valor Time to **Live** para cada disco ou coleção. Esta capacidade eliminou a necessidade de apagar manualmente os registos em Cosmos DB.

> [!IMPORTANT]
> Para completar este tutorial, precisa de criar uma base de dados Cosmos DB e duas coleções (Registo e Erros).

## <a name="create-the-logic-app"></a>Criar a aplicação lógica

O primeiro passo é criar a aplicação lógica e abrir a app no Logic App Designer. Neste exemplo, estamos a usar aplicações lógicas pai-filho. Vamos supor que já criámos o progenitor e vamos criar uma aplicação de lógica infantil.

Porque vamos registar o recorde que sai da Dynamics CRM Online, vamos começar por cima. Temos de usar um gatilho **de pedido** porque a aplicação lógica dos pais desencadeia esta criança.

### <a name="logic-app-trigger"></a>Gatilho de aplicativo lógico

Estamos a utilizar um gatilho **de pedido,** como mostrado no seguinte exemplo:

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

Temos de registar a origem (pedido) do registo do paciente a partir do portal Dynamics CRM Online.

1. Temos de arranjar um novo registo de nomeações da Dynamics CRM Online.

   O gatilho proveniente do CRM fornece-nos o **CRM PatentId**, **tipo de gravação,** **Novo ou Atualizado Record** (valor boolean novo ou atualizar) e **SalesforceId**. O **SalesforceId** pode ser nulo porque só é usado para uma atualização.
   Obtemos o registo de CRM utilizando o CRM **PatientID** e o **Record Type**.

2. Em seguida, precisamos adicionar a nossa app Azure Cosmos DB SQL API **InsertLogEntry** funciona como mostrado aqui no Logic App Designer.

   **Inserir entrada de log**

   ![Screenshot do Logic App Designer mostrando as definições de configuração para InsertLogEntry.](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Inserir entrada de erro**

   ![Screenshot do Logic App Designer mostrando as definições de configuração para CreateErrorRecord.](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Verifique se criar uma falha de gravação**

   ![Screenshot do ecrã CreateErrorRecord no Logic App Designer mostrando os campos para criar uma entrada de erro.](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Código fonte de aplicação lógica

> [!NOTE]
> Os exemplos a seguir são apenas amostras. Como este tutorial se baseia numa implementação agora em produção, o valor de um **Nó fonte** pode não exibir propriedades relacionadas com o agendamento de uma nomeação.> 

### <a name="logging"></a>Registo

A amostra de código de aplicação lógica que se segue mostra como lidar com a exploração madeireira.

#### <a name="log-entry"></a>Entrada de registo

Aqui está o código lógico da origem da aplicação para inserir uma entrada de registo.

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

Aqui está a mensagem de pedido de registo postada na aplicação API.

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

Aqui está a mensagem de resposta de registo da aplicação API.

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

Agora vamos ver os passos de manipulação de erros.

### <a name="error-handling"></a>Processamento de erros

A amostra de código de aplicação lógica que se segue mostra como pode implementar o manuseamento de erros.

#### <a name="create-error-record"></a>Criar registo de erros

Aqui está o código lógico da origem da aplicação para criar um registo de erro.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Inserir erro no Cosmos DB--pedido

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

#### <a name="insert-error-into-cosmos-db--response"></a>Inserir erro no Cosmos DB--resposta

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

#### <a name="salesforce-error-response"></a>Resposta de erro da Salesforce

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Devolva a resposta à app lógica dos pais

Depois de obter a resposta, pode passar a resposta de volta para a aplicação lógica dos pais.

#### <a name="return-success-response-to-parent-logic-app"></a>Resposta de retorno do sucesso à app lógica dos pais

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

#### <a name="return-error-response-to-parent-logic-app"></a>Resposta de erro de retorno à app lógica dos pais

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


## <a name="cosmos-db-repository-and-portal"></a>Repositório e portal Cosmos DB

A nossa solução adicionou capacidades com [a Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portal de gestão de erros

Para visualizar os erros, pode criar uma aplicação web MVC para exibir os registos de erro da Cosmos DB. As operações **List**, **Details**, **Edit**e **Delete** estão incluídas na versão atual.

> [!NOTE]
> Edição: Cosmos DB substitui todo o documento. Os registos mostrados na **Lista** e **nos pontos de** vista são apenas amostras. Não são registos de consulta de pacientes.

Aqui estão exemplos dos detalhes da nossa aplicação MVC criadas com a abordagem previamente descrita.

#### <a name="error-management-list"></a>Lista de gestão de erros
![Lista de erros](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Vista de detalhe de gestão de erros
![Detalhes do Erro](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de gestão de registos

Para visualizar os registos, também criámos uma aplicação web MVC. Aqui estão exemplos dos detalhes da nossa aplicação MVC criadas com a abordagem previamente descrita.

#### <a name="sample-log-detail-view"></a>Vista de detalhes do registo de amostra
![Versão de detalhes de registo](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalhes da aplicação da API

#### <a name="logic-apps-exception-management-api"></a>Gestão de exceções de Apps Lógicas API

A nossa aplicação de gestão de exceção API API de gestão de azure Logic Apps de código aberto fornece funcionalidade como descrito aqui - existem dois controladores:

* **ErrorController** insere um registo de erro (documento) numa coleção DB da Azure Cosmos.
* **LogController** Insere um registo de registo (documento) numa coleção DB da Azure Cosmos.

> [!TIP]
> Ambos os controladores usam `async Task<dynamic>` operações, permitindo que as operações resolvam em tempo de execução, para que possamos criar o esquema DB Azure Cosmos no corpo da operação. 
> 

Todos os documentos em Azure Cosmos DB devem ter uma identificação única. Estamos a usar `PatientId` e adicionar um timetamp que é convertido para um valor de timetamp Unix (duplo). Truncamos o valor para remover o valor fracionado.

Pode ver o código fonte do nosso controlador de erroS API do [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Chamamos a API de uma aplicação lógica utilizando a seguinte sintaxe:

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

A expressão na amostra de código anterior verifica o *estado Create_NewPatientRecord* de **Falhado**.

## <a name="summary"></a>Resumo

* Pode implementar facilmente o registo e o manuseamento de erros numa aplicação lógica.
* Pode utilizar a Azure Cosmos DB como repositório para registos de registos e erros (documentos).
* Pode utilizar o MVC para criar um portal para exibir registos de registos e erros.

### <a name="source-code"></a>Código de origem

O código fonte para a aplicação de gestão de exceções de Apps Lógicas API está disponível neste [repositório GitHub.](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de Gestão de Exceção de Aplicativos Lógicas")

## <a name="next-steps"></a>Passos seguintes

* [Ver mais exemplos e cenários de aplicativos lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Monitorizar aplicações lógicas](../logic-apps/monitor-logic-apps.md)
* [Automatizar a implementação de uma aplicação lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
