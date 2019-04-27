---
title: Web atividade numa fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como pode utilizar a atividade da Web, uma das atividades de fluxo de controlo suportadas pelo Data Factory, para invocar um ponto final REST a partir de um pipeline.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: shlo
ms.openlocfilehash: 7edaa4c673c2cb94dc5bd0245ce66c9fe6a7dd3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764293"
---
# <a name="web-activity-in-azure-data-factory"></a>Atividade Web no Azure Data Factory
A atividade Web pode ser utilizada para chamar um ponto final REST personalizado a partir de um pipeline do Data Factory. Pode transmitir conjuntos de dados e serviços ligados aos quais a atividade tem acesso e que pode consumir.

## <a name="syntax"></a>Sintaxe

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade web | String | Sim
tipo | Tem de ser definido como **WebActivity**. | String | Sim
método | Método de REST API para o ponto de extremidade de destino. | cadeia de caracteres. <br/><br/>Tipos suportados: "GET", "POST", "PUT" | Sim
url | Ponto de extremidade de destino e o caminho | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres). A atividade atingirá o tempo limite em 1 minuto com um erro se não receber uma resposta do ponto final. | Sim
Cabeçalhos | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e o tipo de um pedido: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) | Sim, o cabeçalho Content-type é necessário. `"headers":{ "Content-Type":"application/json"}`
corpo | Representa o payload que é enviado para o ponto final.  | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres). <br/><br/>Ver o esquema do payload de pedido no [esquema de payload de pedido](#request-payload-schema) secção. | É necessário para métodos POST/PUT.
autenticação | Método de autenticação utilizado para chamar o ponto final. Tipos suportados são "Basic ou ClientCertificate." Para obter mais informações, consulte [autenticação](#authentication) secção. Se a autenticação não é necessária, exclua esta propriedade. | Cadeia de caracteres (ou expressão com resultType de cadeia de caracteres) | Não
conjuntos de dados | Lista de conjuntos de dados passados para o ponto final. | Matriz de referências de conjunto de dados. Pode ser uma matriz vazia. | Sim
linkedServices | Lista de serviços ligados são transmitidos ao ponto final. | Matriz de referências de serviço ligado. Pode ser uma matriz vazia. | Sim

> [!NOTE]
> Pontos finais REST, que invoca a atividade web tem de devolver uma resposta do tipo JSON. A atividade atingirá o tempo limite em 1 minuto com um erro se não receber uma resposta do ponto final.

A tabela seguinte mostra os requisitos para o conteúdo JSON:

| Tipo de valor | Corpo do pedido | Corpo da resposta |
|---|---|---|
|Objeto JSON | Suportadas | Suportadas |
|Matriz JSON | Suportadas <br/>(No momento, matrizes JSON não funcionam como resultado de um bug. Uma correção está em curso.) | Não suportado |
| Valor JSON | Suportadas | Não suportado |
| Tipo de não-JSON | Não suportado | Não suportado |
||||

## <a name="authentication"></a>Autenticação

### <a name="none"></a>Nenhuma
Se a autenticação não é necessária, não inclua a propriedade de "autenticação".

### <a name="basic"></a>Básica
Especifique o nome de utilizador e palavra-passe para utilizar com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente
Especifique o conteúdo codificado em base64 de um ficheiro PFX e a palavra-passe.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade Gerida

Especifique o uri do recurso para o qual o token de acesso será será solicitado usando-a identidade gerida do data Factory. Para chamar a API de gestão de recursos do Azure, utilize `https://management.azure.com/`. Para obter mais informações sobre identidades geridas como funciona a ver o [geridos identidades para a página de descrição geral de recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Esquema do payload de pedido
Quando utiliza o método POST/PUT, a propriedade body representa o payload que é enviado para o ponto final. Pode passar os serviços ligados e conjuntos de dados como parte da carga. Este é o esquema para o payload de:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Exemplo
Neste exemplo, a atividade da web no pipeline chama um ponto de final de REST. Ele passa um serviço ligado SQL do Azure e um conjunto de dados SQL do Azure para o ponto final. O ponto final REST, utiliza a cadeia de ligação de SQL do Azure para ligar ao servidor SQL do Azure e devolve o nome da instância do SQL server.

### <a name="pipeline-definition"></a>Definição de pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Valores de parâmetros do pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Código de ponto final do serviço Web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory:

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
