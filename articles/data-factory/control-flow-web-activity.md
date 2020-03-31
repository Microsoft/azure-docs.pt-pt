---
title: Atividade Web na Fábrica de Dados Azure
description: Saiba como pode utilizar a Atividade Web, uma das atividades de fluxo de controlo suportadas pela Data Factory, para invocar um ponto final REST a partir de um pipeline.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: c700c9786f3bec4c79cae904a95deb5fd1c670b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260868"
---
# <a name="web-activity-in-azure-data-factory"></a>Atividade web na Fábrica de Dados Azure
A atividade Web pode ser utilizada para chamar um ponto final REST personalizado a partir de um pipeline do Data Factory. Pode transmitir conjuntos de dados e serviços ligados aos quais a atividade tem acesso e que pode consumir.

> [!NOTE]
> A Atividade Web só pode ligar para URLs expostos publicamente. Não é suportado para URLs que estão hospedados numa rede virtual privada.

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

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade web | Cadeia | Sim
tipo | Deve ser definido para **WebActivity**. | Cadeia | Sim
método | Método API de repouso para o ponto final alvo. | Cadeia. <br/><br/>Tipos suportados: "GET", "POST", "PUT" | Sim
url | Ponto final e caminho | Corda (ou expressão com resultadoTipo de corda). A atividade irá esgotar-se a 1 minuto com um erro se não receber uma resposta do ponto final. | Sim
cabeçalhos | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`e escrever num pedido: . | Corda (ou expressão com resultadoTipo de corda) | Sim, é necessário um cabeçalho do tipo conteúdo. `"headers":{ "Content-Type":"application/json"}`
body | Representa a carga útil que é enviada para o ponto final.  | Corda (ou expressão com resultadoTipo de corda). <br/><br/>Consulte o esquema da carga útil do pedido na secção esquema de [carga útil solicitação.](#request-payload-schema) | Necessário para métodos POST/PUT.
autenticação | Método de autenticação utilizado para chamar o ponto final. Os tipos suportados são "Básico, ou Certificado de Cliente". Para mais informações, consulte a secção [autenticação.](#authentication) Se não for necessária a autenticação, exclua este imóvel. | Corda (ou expressão com resultadoTipo de corda) | Não
conjuntos de dados | A lista de conjuntos de dados passou para o ponto final. | Conjunto de referências de conjuntos de dados. Pode ser uma matriz vazia. | Sim
linkedServices | A lista de serviços ligados passou para o ponto final. | Conjunto de referências de serviço ligadas. Pode ser uma matriz vazia. | Sim

> [!NOTE]
> Rest pontos finais que a atividade web invoca deve devolver uma resposta do tipo JSON. A atividade irá esgotar-se a 1 minuto com um erro se não receber uma resposta do ponto final.

O quadro seguinte mostra os requisitos para o conteúdo da JSON:

| Tipo de valor | Corpo do pedido | Corpo da resposta |
|---|---|---|
|Objeto JSON | Suportado | Suportado |
|Matriz JSON | Suportado <br/>(Atualmente, as matrizes JSON não funcionam como resultado de um inseto. Uma correção está em andamento.) | Não suportado |
| Valor JSON | Suportado | Não suportado |
| Tipo não-JSON | Não suportado | Não suportado |
||||

## <a name="authentication"></a>Autenticação

Abaixo estão os tipos de autenticação suportados na atividade web.

### <a name="none"></a>Nenhuma

Se não for necessária a autenticação, não inclua o imóvel de "autenticação".

### <a name="basic"></a>Básico

Especifique o nome do utilizador e a palavra-passe para usar com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente

Especifique o conteúdo codificado base64 de um ficheiro PFX e a palavra-passe.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade Gerida

Especifique o uri de recurso para o qual o token de acesso será solicitado utilizando a identidade gerida para a fábrica de dados. Para ligar para a API `https://management.azure.com/`de Gestão de Recursos Azure, utilize. Para obter mais informações sobre como funcionam as identidades geridas, consulte as identidades geridas para a página geral dos [recursos do Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se a sua fábrica de dados estiver configurada com um repositório git, deve armazenar as suas credenciais no Cofre de Chaves Azure para utilizar a autenticação básica ou de certificado de cliente. A Azure Data Factory não armazena senhas em git.

## <a name="request-payload-schema"></a>Solicitar esquema de carga útil
Quando utiliza o método POST/PUT, a propriedade do corpo representa a carga útil que é enviada para o ponto final. Pode passar por serviços e conjuntos de dados ligados como parte da carga útil. Aqui está o esquema para a carga:

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
Neste exemplo, a atividade web no pipeline chama um ponto final DE REPOUSO. Passa um serviço ligado ao Azure SQL e um conjunto de dados Azure SQL até ao ponto final. O ponto final DO REST utiliza a cadeia de ligação Azure SQL para ligar ao servidor Azure SQL e devolve o nome da instância do servidor SQL.

### <a name="pipeline-definition"></a>Definição do gasoduto

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

### <a name="pipeline-parameter-values"></a>Valores dos parâmetros do gasoduto

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Código final do serviço web

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

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory:

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
