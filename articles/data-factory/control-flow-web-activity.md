---
title: Atividade web na Fábrica de Dados Azure
description: Saiba como pode utilizar a Web Activity, uma das atividades de fluxo de controlo suportadas pela Data Factory, para invocar um ponto final REST a partir de um oleoduto.
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
ms.openlocfilehash: 95cbb509beba82a14b9f8f8a11c603a6d7b8689d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87280805"
---
# <a name="web-activity-in-azure-data-factory"></a>Atividade web na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


A atividade Web pode ser utilizada para chamar um ponto final REST personalizado a partir de um pipeline do Data Factory. Pode transmitir conjuntos de dados e serviços ligados aos quais a atividade tem acesso e que pode consumir.

> [!NOTE]
> A Atividade Web é suportada para invocar URLs que são alojados numa rede virtual privada e para tirar partido do runtime de integração autoalojado. O runtime de integração deve ter uma linha visual para o ponto final do URL. 

## <a name="syntax"></a>Syntax

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
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

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
name | Nome da atividade web | Cadeia | Sim
tipo | Tem de ser definido para **WebActivity**. | Cadeia | Sim
método | Desarre-se o método API para o ponto final do alvo. | Cadeia. <br/><br/>Tipos suportados: "GET", "POST", "PUT" | Sim
url | Ponto final e caminho | Corda (ou expressão com resultadoTipo de corda). A atividade excederá o tempo limite após um minuto com um erro se não receber uma resposta do ponto final. | Sim
cabeçalhos | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir a língua e escrever num pedido: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Corda (ou expressão com resultadoType de corda) | Sim, é necessário um cabeçalho do tipo conteúdo. `"headers":{ "Content-Type":"application/json"}`
body | Representa a carga útil que é enviada para o ponto final.  | Corda (ou expressão com resultadoTipo de corda). <br/><br/>Consulte o esquema da carga útil do pedido na secção [de esquemas de carga útil do Pedido.](#request-payload-schema) | Requerido para métodos POST/PUT.
autenticação | Método de autenticação utilizado para chamar o ponto final. Os Tipos Suportados são "Básico, ou Certificado cliente". Para mais informações, consulte a secção [autenticação.](#authentication) Se não for necessária a autenticação, exclua este imóvel. | Corda (ou expressão com resultadoType de corda) | Não
conjuntos de dados | Lista de conjuntos de dados passados para o ponto final. | Conjunto de referências de conjunto de dados. Pode ser uma matriz vazia. | Sim
linkedServes | Lista de serviços ligados passados para ponto final. | Conjunto de referências de serviço ligadas. Pode ser uma matriz vazia. | Sim
connectVia | O [tempo de integração](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou o tempo de integração auto-hospedado (se a sua loja de dados estiver numa rede privada). Se esta propriedade não for especificada, o serviço utiliza o tempo de execução de integração Azure padrão. | A referência de tempo de integração. | Não 

> [!NOTE]
> Os pontos finais REST que a atividade Web invoca têm de devolver uma resposta do tipo JSON. A atividade excederá o tempo limite após um minuto com um erro se não receber uma resposta do ponto final.

A tabela a seguir mostra os requisitos para o conteúdo JSON:

| Tipo de valor | Corpo do pedido | Corpo da resposta |
|---|---|---|
|Objeto JSON | Suportado | Suportado |
|Matriz JSON | Suportado <br/>(Atualmente, as matrizes JSON não funcionam como resultado de um inseto. Está em curso uma correção.) | Não suportado |
| Valor JSON | Suportado | Não suportado |
| Tipo não-JSON | Não suportado | Não suportado |
||||

## <a name="authentication"></a>Autenticação

Abaixo estão os tipos de autenticação suportados na atividade web.

### <a name="none"></a>Nenhum

Se não for necessária a autenticação, não inclua o imóvel de "autenticação".

### <a name="basic"></a>Básico

Especifique o nome de utilizador e a palavra-passe para usar com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente

Especifique o conteúdo codificado de base64 de um ficheiro PFX e a palavra-passe.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade Gerida

Especifique o recurso uri para o qual o token de acesso será solicitado usando a identidade gerida para a fábrica de dados. Para ligar para a API de Gestão de Recursos Azure, utilize `https://management.azure.com/` . Para obter mais informações sobre como as identidades geridas funcionam consulte as [identidades geridas para a página geral dos recursos Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se a sua fábrica de dados estiver configurada com um repositório de git, deve armazenar as suas credenciais no Cofre da Chave Azure para utilizar a autenticação básica ou de certificado de cliente. A Azure Data Factory não armazena senhas em git.

## <a name="request-payload-schema"></a>Solicite o esquema de carga útil
Quando utiliza o método POST/PUT, a propriedade do corpo representa a carga útil enviada para o ponto final. Pode passar serviços e conjuntos de dados ligados como parte da carga útil. Aqui está o esquema para a carga útil:

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
Neste exemplo, a atividade web no oleoduto chama um ponto final REST. Passa um serviço ligado Azure SQL e um conjunto de dados Azure SQL para o ponto final. O ponto final REST utiliza a cadeia de ligação Azure SQL para ligar ao servidor lógico SQL e devolve o nome da instância do servidor SQL.

### <a name="pipeline-definition"></a>Definição de gasoduto

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

### <a name="pipeline-parameter-values"></a>Valores do parâmetro do gasoduto

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Código de ponto final do serviço web

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

- [Executar a Atividade do Pipeline](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
