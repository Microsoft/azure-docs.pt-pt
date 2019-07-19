---
title: Atividade da Web no Azure Data Factory | Microsoft Docs
description: Saiba como você pode usar a atividade da Web, uma das atividades de fluxo de controle com suporte pelo Data Factory, para invocar um ponto de extremidade REST de um pipeline.
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
ms.openlocfilehash: f6153bf1162eaa1c7eab2c358977d754695b64fd
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325397"
---
# <a name="web-activity-in-azure-data-factory"></a>Atividade da Web no Azure Data Factory
A atividade Web pode ser utilizada para chamar um ponto final REST personalizado a partir de um pipeline do Data Factory. Pode transmitir conjuntos de dados e serviços ligados aos quais a atividade tem acesso e que pode consumir.

> [!NOTE]
> A atividade da Web pode chamar apenas URLs expostas publicamente. Não há suporte para URLs que são hospedadas em uma rede virtual privada.

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
name | Nome da atividade da Web | Cadeia | Sim
type | Deve ser definido como **webactivity**. | Cadeia | Sim
method | Método de API REST para o ponto de extremidade de destino. | Strings. <br/><br/>Tipos com suporte: "GET", "POST" E "PUT" | Sim
url | Caminho e ponto de extremidade de destino | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres). A atividade atingirá o tempo limite em 1 minuto com um erro se não receber uma resposta do ponto de extremidade. | Sim
Conector | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`:. | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Sim, o cabeçalho Content-Type é necessário. `"headers":{ "Content-Type":"application/json"}`
Conteúdo | Representa a carga que é enviada para o ponto de extremidade.  | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres). <br/><br/>Consulte o esquema da carga de solicitação na seção [esquema de carga de solicitação](#request-payload-schema) . | Necessário para os métodos POST/PUT.
autenticação | Método de autenticação usado para chamar o ponto de extremidade. Os tipos com suporte são "Basic ou ClientCertificate". Para obter mais informações, consulte a seção [autenticação](#authentication) . Se a autenticação não for necessária, exclua essa propriedade. | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Não
datasets | Lista de conjuntos de valores passados para o ponto de extremidade. | Matriz de referências de DataSet. Pode ser uma matriz vazia. | Sim
linkedServices | Lista de serviços vinculados passados para o ponto de extremidade. | Matriz de referências de serviço vinculado. Pode ser uma matriz vazia. | Sim

> [!NOTE]
> Os pontos de extremidade REST que a atividade da Web invoca deve retornar uma resposta do tipo JSON. A atividade atingirá o tempo limite em 1 minuto com um erro se não receber uma resposta do ponto de extremidade.

A tabela a seguir mostra os requisitos para conteúdo JSON:

| Tipo de valor | Corpo do pedido | Corpo da resposta |
|---|---|---|
|Objeto JSON | Suportadas | Suportadas |
|Matriz JSON | Suportadas <br/>(No momento, as matrizes JSON não funcionam como resultado de um bug. Uma correção está em andamento.) | Não suportado |
| Valor JSON | Suportadas | Não suportado |
| Tipo não JSON | Não suportado | Não suportado |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Nenhum
Se a autenticação não for necessária, não inclua a propriedade "Authentication".

### <a name="basic"></a>Básica
Especifique o nome de usuário e a senha a serem usados com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado do cliente
Especifique o conteúdo codificado na Base64 de um arquivo PFX e a senha.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade Gerida

Especifique o URI de recurso para o qual o token de acesso será solicitado usando a identidade gerenciada para o data factory. Para chamar a API de gerenciamento de recursos do `https://management.azure.com/`Azure, use. Para obter mais informações sobre como as identidades gerenciadas funcionam, consulte a [página Visão geral de identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Esquema de carga de solicitação
Quando você usa o método POST/PUT, a Propriedade Body representa a carga que é enviada para o ponto de extremidade. Você pode passar os serviços vinculados e conjuntos de valores como parte da carga. Aqui está o esquema para a carga:

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
Neste exemplo, a atividade da Web no pipeline chama um ponto de extremidade REST. Ele passa um serviço vinculado do SQL do Azure e um conjunto de uma do Azure SQL para o ponto de extremidade. O ponto de extremidade REST usa a cadeia de conexão SQL do Azure para se conectar ao servidor SQL do Azure e retorna o nome da instância do SQL Server.

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

### <a name="pipeline-parameter-values"></a>Valores de parâmetro de pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Código do ponto de extremidade do serviço Web

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
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
