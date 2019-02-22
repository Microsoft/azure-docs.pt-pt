---
title: Instruções de API do REST de monitorização do Azure
description: Como autenticar os pedidos e utilizar a API de REST do Azure Monitor para obter as definições de métricas disponíveis e valores de métrica.
author: mcollier
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: mcollier
ms.subservice: ''
ms.openlocfilehash: 707c04c22e54220f3020b5897c364318b427267b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586598"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Instruções de API do REST de monitorização do Azure

Este artigo mostra como efetuar a autenticação para que seu código pode utilizar o [referência da API do REST do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

A API do Azure Monitor permite obter automaticamente as definições de métricas de predefinidas disponíveis, granularidade e valores de métrica. Os dados podem ser salvas num arquivo de dados separado, como a base de dados do Azure SQL, Azure Cosmos DB ou o Azure Data Lake. A partir daí, análises adicionais podem ser executadas conforme necessário.

Além de trabalhar com vários pontos de dados de métrica, a API de Monitor também torna possível listar regras de alerta, ver registos de atividades e muito mais. Para obter uma lista completa de operações disponíveis, consulte a [referência da API do REST do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Pedidos de autenticação do Azure Monitor

A primeira etapa é autenticar o pedido.

Todas as tarefas executadas em relação a API de Monitor do Azure, utilize o modelo de autenticação do Azure Resource Manager. Por conseguinte, todos os pedidos têm de ser autenticados com o Azure Active Directory (Azure AD). Uma abordagem para autenticar a aplicação de cliente é criar um Azure AD principal de serviço e obter o token de autenticação (JWT). O script de exemplo seguinte demonstra a criação de um serviço do Azure AD principal através do PowerShell. Para obter instruções mais detalhadas, consulte a documentação sobre [com o Azure PowerShell para criar um principal de serviço para aceder aos recursos](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Também é possível [criar um principal de serviço através do portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API do Azure Monitor, a aplicação cliente deve utilizar o principal de serviço criado anteriormente para autenticar. O script do PowerShell de exemplo seguinte mostra uma abordagem, utilizando o [Active Directory Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md) (ADAL) para obter o token de autenticação do JWT. O token JWT é passado como parte de um parâmetro de autorização de HTTP em pedidos para a API de REST do Azure Monitor.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Após a autenticação, pode, em seguida, executar consultas em relação a API de REST do Azure Monitor. Existem duas consultas úteis:

1. Lista as definições de métricas para um recurso
2. Obter os valores de métrica

> [!NOTE]
> Para obter informações adicionais sobre a autenticação com a API de REST do Azure, consulte a [referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Obter as definições de métricas multidimensional API)

Utilize o [definições de métrica de Monitor de Azure REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) aceder à lista de métricas que estão disponíveis para um serviço.

**Método**: GET

**URI do pedido**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por exemplo, para obter as definições de métricas para uma conta de armazenamento do Azure, a solicitação seria a seguinte aparência:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Para obter as definições de métricas com as métricas multidimensionais do Azure Monitor REST API, utilize "2018-01-01" como a versão de API.
>
>

O corpo da resposta JSON resultante seria semelhante ao seguinte exemplo: (Observe que a segunda métrica tem dimensões)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Obter valores de dimensão (API multidimensional)

Assim que as definições de métricas disponíveis são conhecidas, poderão existir algumas métricas com dimensões. Antes de consulta para a métrica que pode querer detetar que o intervalo de valores tem uma dimensão. Com base nestes valores de dimensão, em seguida, pode optar por filtrar ou as métricas com base nos valores de dimensão ao consultar as métricas de segmento.  Utilize o [API de REST do Azure Monitor métricas](https://docs.microsoft.com/rest/api/monitor/metrics) para atingir esse objetivo.

Utilize nome "value" a métrica (não o ' localizedValue") para todos os pedidos de filtragem. Se forem especificados não existem filtros, é devolvida a métrica predefinida. A utilização desta API permite apenas uma dimensão ter um filtro de caráter universal.

> [!NOTE]
> Para obter os valores de dimensão utilizando a API de REST do Azure Monitor, utilize "2018-01-01" como a versão de API.
>
>

**Método**: GET

**URI do pedido**: https://management.azure.com/subscriptions/ *{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/*{resource-provider-namespace}* / *{tipo de recurso}*/*{nome do recurso}*/providers/microsoft.insights/metrics?metricnames=*{métrica}*& timespan =*{starttime/endtime}*& $filter =*{filtro}*& resultType = metadados e a api-version =*{apiVersion}*

Por exemplo, para obter a lista de valores de dimensão que foram emitidos para a "dimensão de nome da API" para a métrica "Transações", em que a dimensão de GeoType = 'Primary' durante o intervalo de tempo especificado, a solicitação seria da seguinte forma:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

O corpo da resposta JSON resultante seria semelhante ao seguinte exemplo:

```JSON
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Obter valores de métrica (API multidimensional)

Assim que as definições de métricas disponíveis e os valores de dimensão possível são conhecidas, em seguida, é possível recuperar os valores de métrica relacionados.  Utilize o [API de REST do Azure Monitor métricas](https://docs.microsoft.com/rest/api/monitor/metrics) para atingir esse objetivo.

Utilize nome "value" a métrica (não o ' localizedValue") para todos os pedidos de filtragem. Se forem especificados não existem filtros de dimensão, a métrica de agregados rolled cópia de segurança é devolvida. Se uma consulta métrica retornar vários série de tempo, em seguida, pode utilizar os parâmetros de consulta 'Principais' e 'OrderBy' para retornar uma lista ordenada limitada de série de tempo.

> [!NOTE]
> Para obter valores de métricas multidimensionais através da API de REST do Azure Monitor, utilize "2018-01-01" como a versão de API.
>
>

**Método**: GET

**URI do pedido**: https://management.azure.com/subscriptions/ *{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/*{resource-provider-namespace}* / *{tipo de recurso}*/*{nome do recurso}*/providers/microsoft.insights/metrics?metricnames=*{métrica}*& timespan =*{starttime/endtime}*& $filter =*{filtro}*& intervalo =*{timeGrain}*& agregação =*{ aggreation}*& api-version =*{apiVersion}*

Por exemplo, para recuperar a parte superior 3 APIs, descendente de valor, pelo número de transações durante um intervalo de 5 min, onde o GeotType foi 'Primary', a solicitação seria da seguinte forma:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

O corpo da resposta JSON resultante seria semelhante ao seguinte exemplo:

```JSON
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Obter as definições de métricas

Utilize o [definições de métrica de Monitor de Azure REST API](https://msdn.microsoft.com/library/mt743621.aspx) aceder à lista de métricas que estão disponíveis para um serviço.

**Método**: GET

**URI do pedido**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por exemplo, para obter as definições de métricas para uma aplicação de lógica do Azure, a solicitação seria a seguinte aparência:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Para obter as definições de métricas com a API de REST do Azure Monitor, utilize "2016-03-01" como a versão de API.
>
>

O corpo da resposta JSON resultante seria semelhante ao seguinte exemplo:

```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Para obter mais informações, consulte a [lista as definições de métricas para um recurso na API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) documentação.

## <a name="retrieve-metric-values"></a>Obter valores de métrica

Assim que as definições de métricas disponíveis são conhecidas, em seguida, é possível recuperar os valores de métrica relacionados. Utilize nome "value" a métrica (não o ' localizedValue") para todos os pedidos de filtragem (por exemplo, recuperar os pontos de dados de métrica"CpuTime"e"Pedidos"). Se forem especificados não existem filtros, é devolvida a métrica predefinida.

> [!NOTE]
> Para obter valores de métrica utilizando a API de REST do Azure Monitor, utilize "2016-09-01" como a versão de API.
>
>

**Método**: GET

**URI do pedido**: https://management.azure.com/subscriptions/ *{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/*{resource-provider-namespace}* / *{tipo de recurso}*/*{nome do recurso}*/providers/microsoft.insights/metrics?$filter=*{filtro}*& api-version =*{apiVersion}*

Por exemplo, para obter os pontos de dados métricos RunsSucceeded para o intervalo de tempo específico e para um grão de tempo de 1 hora, a solicitação seria da seguinte forma:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

O corpo da resposta JSON resultante seria semelhante ao seguinte exemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Para obter vários pontos de dados ou agregação, adicione os nomes de definição de métrica e tipos de agregação para o filtro, conforme mostrado no exemplo a seguir:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

O corpo da resposta JSON resultante seria semelhante ao seguinte exemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Utilizar ARMClient

Uma abordagem adicional é usar [ARMClient](https://github.com/projectkudu/armclient) no seu computador Windows. ARMClient processa automaticamente a autenticação do Azure AD (e o token resultante do JWT). Os passos seguintes descrevem a utilização de ARMClient para recuperação de dados de métrica:

1. Instale [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/armclient).
2. Na janela do terminal, escreva *armclient.exe início de sessão*. Se o fizer, pede-lhe para iniciar sessão no Azure.
3. Tipo de *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Tipo de *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Por exemplo, para poder recuperar as definições de métricas para uma aplicação de lógica específica, emita o seguinte comando:

```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Obter o ID de recurso

Com a API REST realmente pode ajudar a compreender as definições de métricas disponíveis, a granularidade e a valores relacionados. Que informações são útil quando utilizar o [biblioteca de gestão do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Para o código anterior, o ID de recurso a utilizar é o caminho completo para o recurso do Azure pretendido. Por exemplo, a consulta em relação a uma aplicação Web do Azure, o ID de recurso deve ser:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista seguinte contém alguns exemplos de formatos de ID de recurso para vários recursos do Azure:

* **IoT Hub** -/subscriptions/*{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Conjunto elástico de SQL** -/subscriptions/*{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Sql/servers/*{conjunto-db}*/elasticpools/*{sql-pool-name}*
* **Base de dados do SQL (v12)** -/subscriptions/*{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Sql/servers/*{servidor-name}*/databases/*{da base de dados-name}*
* **Do Service Bus** -/subscriptions/*{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.ServiceBus/*{namespace}* / *{servicebus-name}*
* **Os conjuntos de dimensionamento de máquinas virtuais** -/subscriptions/*{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{vm-name}*
* **VMs** -/subscriptions/*{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Os Hubs de eventos** -/subscriptions/*{subscrição-id}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.EventHub/namespaces/*{ eventhub-namespace}*

Existem abordagens alternativas para obter o ID de recurso, incluindo a utilização do Explorador de recursos do Azure, visualizar o recurso pretendido, no portal do Azure e através do PowerShell ou a CLI do Azure.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure

Para localizar o ID de recurso de um recurso desejado, uma abordagem útil consiste em utilizar o [Explorador de recursos do Azure](https://resources.azure.com) ferramenta. Navegue para o recurso pretendido e, em seguida, examinar o ID apresentado, como na captura de ecrã seguinte:

![ALT "Explorador de recursos do Azure"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure

O ID de recurso também pode ser obtido a partir do portal do Azure. Para tal, navegue para o recurso pretendido e, em seguida, selecione propriedades. O ID de recurso é apresentado na secção de propriedades, conforme mostrado na captura de ecrã seguinte:

![ALT "ID de recurso apresentado no painel de propriedades no portal do Azure"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

O ID de recurso pode ser obtido utilizando cmdlets do Azure PowerShell também. Por exemplo, para obter o ID de recurso para uma aplicação de lógica do Azure, execute o cmdlet Get-AzureLogicApp, como no exemplo seguinte:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

O resultado deve ser semelhante ao seguinte exemplo:

```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>CLI do Azure

Para obter o ID de recurso para uma conta de armazenamento do Azure com a CLI do Azure, execute o comando de "az storage account show", conforme mostrado no exemplo a seguir:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

O resultado deve ser semelhante ao seguinte exemplo:

```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> O Azure Logic Apps não são ainda disponível através da CLI do Azure, portanto, uma conta de armazenamento do Azure é mostrada no exemplo anterior.
>
>

## <a name="retrieve-activity-log-data"></a>Obter dados de registo de atividade

Para além das definições de métricas e os valores relacionados, também é possível utilizar a API de REST do Azure Monitor para obter informações interessantes adicionais relacionadas com os recursos do Azure. Por exemplo, é possível consultar [registo de atividades](https://msdn.microsoft.com/library/azure/dn931934.aspx) dados. O exemplo a seguir demonstra como utilizar a API de REST do Azure Monitor para consultar dados de registo de atividade dentro de um intervalo de datas específico para uma subscrição do Azure:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Passos Seguintes

* Reveja os [descrição geral da monitorização](../../azure-monitor/overview.md).
* Ver os [suportado métricas com o Azure Monitor](metrics-supported.md).
* Reveja os [do Microsoft Azure monitorizar referência da API REST](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Reveja os [biblioteca de gestão do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).