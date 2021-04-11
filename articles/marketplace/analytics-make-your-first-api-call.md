---
title: Faça a sua primeira chamada de API para aceder a dados de análise de marketplace comercial
description: Exemplos para aprender a usar a API para aceder a dados de análise de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9e5fbdfca80d19f026a014a89ffbf137bacb521c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639580"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Faça a sua primeira chamada de API para aceder a dados de análise de marketplace comercial

Para obter uma lista das APIs para aceder a dados de análise de mercado comercial, consulte [APIs para aceder a dados de análise de mercado comercial](analytics-available-apis.md). Antes de fazer a sua primeira chamada da API, certifique-se de que cumpriu os [requisitos necessários](analytics-prerequisites.md) para aceder programaticamente aos dados de análise de marketplace comercial.

## <a name="token-generation"></a>Geração Token

Antes de ligar para qualquer um dos métodos, você deve primeiro obter um token de acesso Azure Ative Directory (Azure AD). Você precisa passar o token de acesso Azure para o cabeçalho de autorização de cada método na API. Depois de obter um token de acesso, tem 60 minutos para usá-lo antes de expirar. Após o fim do token, pode refrescar o token e continuar a usá-lo para mais chamadas para a API.

Consulte um pedido de amostra abaixo para gerar um token. Os três valores que são necessários para gerar o símbolo `clientId` `clientSecret` são, e `tenantId` . O `resource` parâmetro deve ser definido para `https://graph.windows.net` .

***Exemplo de pedido:***

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Exemplo de resposta:***

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Para obter mais informações sobre como obter um token AD Azure para a sua aplicação, consulte [os dados de análise do Access através dos serviços da Loja.](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token)

## <a name="programmatic-api-call"></a>Chamada de API programática

Depois de obter o Token AD Azure, conforme descrito na secção anterior, siga estes passos para criar o seu primeiro relatório de acesso programático.

Os dados podem ser descarregados a partir dos seguintes conjuntos de dados (datasetName):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

Nas seguintes secções, veremos exemplos de como aceder programáticamente `OrderId` a partir do conjunto de dados ISVOrder.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Passo 1: Faça uma chamada REST utilizando a API Get Datasets

A resposta da API fornece o nome do conjunto de dados de onde pode descarregar o relatório. Para o conjunto de dados específico, a resposta API também fornece a lista de colunas selecionáveis que podem ser usadas para o seu modelo de relatório personalizado. Também pode consultar as seguintes tabelas para obter os nomes das colunas:

- [Tabela de detalhes de encomendas](orders-dashboard.md#orders-details-table)
- [Tabela de detalhes de utilização](usage-dashboard.md#usage-details-table)
- [Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)
- [Tabela de detalhes de insights de mercado](insights-dashboard.md#marketplace-insights-details-table)

***Exemplo de pedido:***

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Exemplo de resposta:***

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Passo 2: Criar a consulta personalizada

Neste passo, usaremos o ID da Ordem do Relatório de Encomendas para criar uma consulta personalizada para o relatório que queremos. O `timespan` predefinido se não for especificado na consulta é de seis meses.

***Exemplo de pedido:***

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Exemplo de resposta:***

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Na execução bem sucedida da consulta, é gerada uma `queryId` que precisa de ser usada para gerar o relatório.

### <a name="step-3-execute-test-query-api"></a>Passo 3: Executar consulta de teste API

Neste passo, vamos usar a consulta de teste API para obter as 100 melhores linhas para a consulta que foi criada.

***Exemplo de pedido:***

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Exemplo de resposta:***

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Passo 4: Criar o relatório

Neste passo, usaremos o anteriormente gerado `QueryId` para criar o relatório.

***Exemplo de pedido:***

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Quadro 1: Descrição dos parâmetros utilizados neste exemplo de pedido**_

| Parâmetro | Descrição |
| ------------ | ------------- |
| `Description` | Forneça uma breve descrição do relatório que está a ser gerado. |
| `QueryId` | Este é o `queryId` que foi gerado quando a consulta foi criada no Passo 2: Criar consulta personalizada. |
| `StartTime` | Quando começou a primeira execução do relatório. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório. |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório. |
| `Format` | Os formatos de ficheiroS CSV e TSV são suportados. |
|||

***Exemplo de resposta:***

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Na execução bem sucedida, é gerado um `reportId` que precisa ser usado para agendar um download do relatório.

### <a name="step-5-execute-report-executions-api"></a>Passo 5: Executar relatório execuções execuções API

Para obter a localização segura (URL) do relatório, vamos agora executar a API de Execuções de Relatório.

***Exemplo de pedido:***

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Exemplo de resposta:***

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Passos seguintes

- Você pode experimentar as APIs através do [URL da API swagger](https://swagger.io/docs/specification/api-host-and-base-path/)
- [Paradigma de acesso programático](analytics-programmatic-access.md)
