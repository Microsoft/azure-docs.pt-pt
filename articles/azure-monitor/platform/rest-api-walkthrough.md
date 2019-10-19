---
title: Instruções da API REST de monitoramento do Azure
description: Como autenticar solicitações e usar a API REST do Azure Monitor para recuperar as definições de métricas e os valores de métrica disponíveis.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 03/19/2018
ms.openlocfilehash: 68c90f6c763fe7cd634aee886c5c8c6b8153253e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551822"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Instruções da API REST de monitoramento do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra como executar a autenticação para que seu código possa usar a [referência de API REST do Microsoft Azure monitor](https://docs.microsoft.com/rest/api/monitor/).

A API Azure Monitor possibilita recuperar programaticamente as definições de métrica padrão disponíveis, a granularidade e os valores de métrica. Os dados podem ser salvos em um armazenamento de dados separado, como o banco de dados SQL do Azure, Azure Cosmos DB ou Azure Data Lake. A partir daí, uma análise adicional pode ser executada conforme necessário.

Além de trabalhar com vários pontos de dados de métrica, a API do monitor também torna possível listar as regras de alerta, exibir logs de atividades e muito mais. Para obter uma lista completa das operações disponíveis, consulte a [referência da API REST do Microsoft Azure monitor](https://docs.microsoft.com/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Autenticando solicitações de Azure Monitor

A primeira etapa é autenticar a solicitação.

Todas as tarefas executadas em relação à API de Azure Monitor usam o modelo de autenticação Azure Resource Manager. Portanto, todas as solicitações devem ser autenticadas com Azure Active Directory (Azure AD). Uma abordagem para autenticar o aplicativo cliente é criar uma entidade de serviço do Azure AD e recuperar o token de autenticação (JWT). O exemplo de script a seguir demonstra como criar uma entidade de serviço do Azure AD por meio do PowerShell. Para obter um passo a passo mais detalhado, consulte a documentação sobre como [usar Azure PowerShell para criar uma entidade de serviço para acessar recursos](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Também é possível [criar uma entidade de serviço por meio do portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API de Azure Monitor, o aplicativo cliente deve usar a entidade de serviço criada anteriormente para autenticar. O exemplo de script do PowerShell a seguir mostra uma abordagem, usando o [biblioteca de autenticação do Active Directory](../../active-directory/develop/active-directory-authentication-libraries.md) (Adal) para obter o token de autenticação JWT. O token JWT é passado como parte de um parâmetro de autorização HTTP em solicitações para a API REST do Azure Monitor.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

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

Após a autenticação, as consultas podem ser executadas em relação à API REST do Azure Monitor. Há duas consultas úteis:

1. Listar as definições de métrica para um recurso
2. Recuperar os valores de métrica

> [!NOTE]
> Para obter informações adicionais sobre como autenticar com a API REST do Azure, consulte a [referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Recuperar as definições de métrica (API multidimensional)

Use a [API REST de definições de métrica Azure monitor](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) para acessar a lista de métricas que estão disponíveis para um serviço.

**Método**: Get

**URI de solicitação**: https: \/ \/management. Azure.com/subscriptions/ *{SubscriptionId}* /resourceGroups/ *{resourceGroupName}* /Providers/ *{resourceProviderNamespace}* / *{ResourceType}* / *{resourceName}* /Providers/Microsoft.insights/metricDefinitions? API-Version = *{apiVersion}*

Por exemplo, para recuperar as definições de métrica para uma conta de armazenamento do Azure, a solicitação seria exibida da seguinte maneira:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Para recuperar as definições de métrica usando a API REST de métricas de Azure Monitor multidimensionais, use "2018-01-01" como a versão de API.
>
>

O corpo de resposta JSON resultante seria semelhante ao exemplo a seguir: (Observe que a segunda métrica tem dimensões)

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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Recuperar valores de dimensão (API multidimensional)

Depois que as definições de métrica disponíveis são conhecidas, pode haver algumas métricas que têm dimensões. Antes de consultar a métrica, talvez você queira descobrir qual é o intervalo de valores de uma dimensão. Com base nesses valores de dimensão, você pode optar por filtrar ou segmentar as métricas com base nos valores de dimensão ao consultar as métricas.  Use a [API REST de métricas Azure monitor](https://docs.microsoft.com/rest/api/monitor/metrics) para conseguir isso.

Use o nome da métrica ' value ' (não o ' localizador ') para quaisquer solicitações de filtragem. Se nenhum filtro for especificado, a métrica padrão será retornada. O uso dessa API só permite que uma dimensão tenha um filtro curinga.

> [!NOTE]
> Para recuperar valores de dimensão usando a API REST do Azure Monitor, use "2018-01-01" como a versão da API.
>
>

**Método**: Get

**URI da solicitação**: https \://Management.Azure.com/subscriptions/ *{Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/ *{Resource-Provider-namespace}* / *{Resource-Type}* / *{nome-do-recurso}* /Providers/Microsoft.insights/Metrics de métricas = *{Metric}* & TimeSpan = *{starttime/EndTime}* & $Filter = *{filtro}* & ResultType = metadados & API-Version = *{ apiVersion}*

Por exemplo, para recuperar a lista de valores de dimensão que foram emitidos para a ' dimensão de nome da API ' para a métrica ' transações ', em que a dimensão geotipo = ' primário ' durante o intervalo de tempo especificado, a solicitação seria a seguinte:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

O corpo de resposta JSON resultante seria semelhante ao exemplo a seguir:

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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Recuperar valores de métrica (API multidimensional)

Depois que as definições de métricas disponíveis e os possíveis valores de dimensão forem conhecidos, será possível recuperar os valores de métrica relacionados.  Use a [API REST de métricas Azure monitor](https://docs.microsoft.com/rest/api/monitor/metrics) para conseguir isso.

Use o nome da métrica ' value ' (não o ' localizador ') para quaisquer solicitações de filtragem. Se nenhum filtro de dimensão for especificado, a métrica Agregada acumulada será retornada. Se uma consulta de métrica retornar várias séries temporais, você poderá usar os parâmetros de consulta ' top ' e ' OrderBy ' para retornar uma lista ordenada limitada de timeseries.

> [!NOTE]
> Para recuperar valores de métrica multidimensionais usando a API REST do Azure Monitor, use "2018-01-01" como a versão da API.
>
>

**Método**: Get

**URI da solicitação**: *https://management.azure.com/subscriptions/ {Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/ *{Resource-Provider-namespace}* / *{tipo de recurso}* / *{nome-do-recurso}* /Providers/ Microsoft. insights/métricas? métricanames = *{métrica}* & TimeSpan = *{StartTime/endtime}* & $Filter = *{filtro}* & intervalo = *{timegranular}* & agregação = *{aggreation}* & API-Version = *{apiVersion}*

Por exemplo, para recuperar as três principais APIs, em valor decrescente, pelo número de ' Transactions ' durante um intervalo de 5 minutos, em que GeotType era ' Primary ', a solicitação seria a seguinte:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

O corpo de resposta JSON resultante seria semelhante ao exemplo a seguir:

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

## <a name="retrieve-metric-definitions"></a>Recuperar definições de métrica

Use a [API REST de definições de métrica Azure monitor](https://msdn.microsoft.com/library/mt743621.aspx) para acessar a lista de métricas que estão disponíveis para um serviço.

**Método**: Get

**URI de solicitação**: https: \/ \/management. Azure.com/subscriptions/ *{SubscriptionId}* /resourceGroups/ *{resourceGroupName}* /Providers/ *{resourceProviderNamespace}* / *{ResourceType}* / *{resourceName}* /Providers/Microsoft.insights/metricDefinitions? API-Version = *{apiVersion}*

Por exemplo, para recuperar as definições de métrica para um aplicativo lógico do Azure, a solicitação seria exibida da seguinte maneira:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Para recuperar as definições de métrica usando a API REST do Azure Monitor, use "2016-03-01" como a versão da API.
>
>

O corpo de resposta JSON resultante seria semelhante ao exemplo a seguir:

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

Para obter mais informações, consulte a [lista de definições de métrica para um recurso em Azure monitor documentação da API REST](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperar valores de métrica

Depois que as definições de métrica disponíveis são conhecidas, é possível recuperar os valores de métrica relacionados. Use o nome da métrica ' value ' (não o ' localizador ') para quaisquer solicitações de filtragem (por exemplo, recupere os pontos de dados de métrica ' CpuTime ' e ' solicitações '). Se nenhum filtro for especificado, a métrica padrão será retornada.

> [!NOTE]
> Para recuperar valores de métrica usando a API REST do Azure Monitor, use "2016-09-01" como a versão da API.
>
>

**Método**: Get

**URI da solicitação**: *https://management.azure.com/subscriptions/ {Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/ *{Resource-Provider-namespace}* / *{tipo de recurso}* / *{nome-do-recurso}* /Providers/ Microsoft. insights/métricas? $filter = *{filtro}* & API-Version = *{apiVersion}*

Por exemplo, para recuperar os pontos de dados da métrica RunsSucceeded para o intervalo de tempo determinado e, por uma hora, de 1 hora, a solicitação seria a seguinte:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

O corpo de resposta JSON resultante seria semelhante ao exemplo a seguir:

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

Para recuperar vários pontos de dados ou de agregação, adicione os nomes de definição de métrica e os tipos de agregação ao filtro, como mostrado no exemplo a seguir:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

O corpo de resposta JSON resultante seria semelhante ao exemplo a seguir:

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

### <a name="use-armclient"></a>Usar ARMClient

Uma abordagem adicional é usar o [ARMClient](https://github.com/projectkudu/armclient) em seu computador Windows. ARMClient manipula a autenticação do Azure AD (e o token JWT resultante) automaticamente. As etapas a seguir descrevem o uso de ARMClient para recuperar dados de métrica:

1. Instale o [Chocolatey](https://chocolatey.org/) e o [ARMClient](https://github.com/projectkudu/armclient).
2. Em uma janela de terminal, digite *armclient. exe login*. Isso solicitará que você faça logon no Azure.
3. Digite *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.insights/metricdefinitions? API-Version = 2016-03-01*
4. Digite *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.insights/Metrics? API-Version = 2016-09-01*

Por exemplo, para recuperar as definições de métrica para um aplicativo lógico específico, emita o seguinte comando:

```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Recuperar a ID do recurso

Usar a API REST pode realmente ajudar a entender as definições de métrica disponíveis, a granularidade e os valores relacionados. Essas informações são úteis ao usar a [biblioteca de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Para o código anterior, a ID de recurso a ser usada é o caminho completo para o recurso do Azure desejado. Por exemplo, para consultar um aplicativo Web do Azure, a ID do recurso seria:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista a seguir contém alguns exemplos de formatos de ID de recurso para vários recursos do Azure:

* **Hub IOT** -/subscriptions/ *{Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/Microsoft.Devices/IotHubs/ *{IOT-Hub-Name}*
* **Pool SQL elástico** -/subscriptions/ *{Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/Microsoft.SQL/Servers/ *{pool-DB}* /elasticpools/ *{SQL-pool-name}*
* **Banco de dados SQL (V12)** -/subscriptions/ *{Subscription-ID}* /resourceGroups/ *{Resource-Group-Name}* /Providers/Microsoft.SQL/Servers/ *{Server-Name}* /databases/ *{nome-do-banco de dados}*
* **Barramento de serviço** - */subscriptions/{Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/Microsoft.ServiceBus/ *{namespace}* / *{ServiceBus-Name}*
* **Conjuntos de dimensionamento de máquinas virtuais** -/subscriptions/ *{Subscription-ID}* /resourceGroups/ *{Resource-Group-Name}* /Providers/Microsoft.Compute/virtualMachineScaleSets/ *{VM-Name}*
* **VMs** -/subscriptions/ *{Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/Microsoft.Compute/virtualMachines/ *{nome* -da-VM}
* **Hubs de eventos** - */subscriptions/{Subscription-ID}* /resourceGroups/ *{nome-do-grupo-de-recursos}* /Providers/Microsoft.EventHub/namespaces/ *{EventHub-namespace}*

Há abordagens alternativas para recuperar a ID do recurso, incluindo o uso de Azure Resource Explorer, a exibição do recurso desejado no portal do Azure e o PowerShell ou o CLI do Azure.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure

Para localizar a ID de recurso de um recurso desejado, uma abordagem útil é usar a ferramenta de [Azure Resource Explorer](https://resources.azure.com) . Navegue até o recurso desejado e examine a ID mostrada, como na seguinte captura de tela:

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure

A ID do recurso também pode ser obtida no portal do Azure. Para fazer isso, navegue até o recurso desejado e, em seguida, selecione Propriedades. A ID do recurso é exibida na seção Propriedades, como mostrado na seguinte captura de tela:

![Alt "ID do recurso exibida na folha de propriedades no portal do Azure"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

A ID do recurso também pode ser recuperada usando cmdlets Azure PowerShell. Por exemplo, para obter a ID de recurso para um aplicativo lógico do Azure, execute o cmdlet Get-AzureLogicApp, como no exemplo a seguir:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

O resultado deve ser semelhante ao exemplo a seguir:

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

Para recuperar a ID de recurso para uma conta de armazenamento do Azure usando o CLI do Azure, execute o comando `az storage account show`, conforme mostrado no exemplo a seguir:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

O resultado deve ser semelhante ao exemplo a seguir:

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
> Os aplicativos lógicos do Azure ainda não estão disponíveis por meio do CLI do Azure, portanto, uma conta de armazenamento do Azure é mostrada no exemplo anterior.
>
>

## <a name="retrieve-activity-log-data"></a>Recuperar dados do log de atividades

Além das definições de métrica e valores relacionados, também é possível usar a API REST Azure Monitor para recuperar informações interessantes adicionais relacionadas aos recursos do Azure. Por exemplo, é possível consultar os dados do [log de atividades](https://msdn.microsoft.com/library/azure/dn931934.aspx) . O exemplo a seguir demonstra como usar a API REST Azure Monitor para consultar dados do log de atividades em um intervalo de datas específico para uma assinatura do Azure:

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Passos seguintes

* Examine a [visão geral do monitoramento](../../azure-monitor/overview.md).
* Exiba as [métricas com suporte com Azure monitor](metrics-supported.md).
* Examine a [referência da API REST do monitor de Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Examine a [biblioteca de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).