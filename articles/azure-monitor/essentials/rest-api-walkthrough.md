---
title: Instruções da API REST de Monitorização do Azure
description: Como autenticar pedidos e utilizar a API do Monitor Azure para recuperar definições métricas métricas disponíveis e valores métricos.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref
ms.openlocfilehash: a7cd6ff7c0c3b5d4bee859ef288f16673ebe0835
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033083"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Instruções da API REST de Monitorização do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra-lhe como executar a autenticação para que o seu código possa utilizar o [Microsoft Azure Monitor API Reference](/rest/api/monitor/).

A Azure Monitor API permite recuperar programáticamente as definições métricas padrão disponíveis, granularidade e valores métricos. Os dados podem ser guardados numa loja de dados separada, como a Base de Dados Azure SQL, Azure Cosmos DB ou Azure Data Lake. A partir daí, a análise adicional pode ser realizada conforme necessário.

Além de trabalhar com vários pontos de dados métricos, a API monitor também permite listar regras de alerta, ver registos de atividade, e muito mais. Para obter uma lista completa das operações disponíveis, consulte a [Referência API do Microsoft Azure Monitor REST](/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Autenticação de pedidos do Monitor Azure

O primeiro passo é autenticar o pedido.

Todas as tarefas executadas contra a AZure Monitor API utilizam o modelo de autenticação Azure Resource Manager. Portanto, todos os pedidos devem ser autenticados com Azure Ative Directory (Azure AD). Uma abordagem para autenticar a aplicação do cliente é criar um principal de serviço AD Azure e recuperar o sinal de autenticação (JWT). O seguinte script de amostra demonstra a criação de um principal de serviço Azure AD via PowerShell. Para obter um walk-through mais detalhado, consulte a documentação sobre a utilização do [Azure PowerShell para criar um principal serviço para aceder aos recursos.](/powershell/azure/create-azure-service-principal-azureps) Também é possível [criar um principal de serviço através do portal Azure.](../../active-directory/develop/howto-create-service-principal-portal.md)

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

Para consultar a API do Azure Monitor, a aplicação do cliente deve utilizar o principal de serviço previamente criado para autenticar. O seguinte exemplo O script PowerShell mostra uma abordagem, utilizando a [Biblioteca de Autenticação de Diretório Ativo](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) para obter o token de autenticação JWT. O token JWT é passado como parte de um parâmetro de autorização HTTP em pedidos à AZure Monitor REST API.

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

Após a autenticação, as consultas podem ser executadas contra a API do Monitor Azure. Existem duas consultas úteis:

1. Listar as definições métricas para um recurso
2. Recuperar os valores métricos

> [!NOTE]
> Para obter informações adicionais sobre a autenticação com a API Azure REST, consulte a [Referência API AZURE REST](/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Recuperar definições métricas (API multidimensional)

Utilize as [definições Azure Monitor Metric REST API](/rest/api/monitor/metricdefinitions) para aceder à lista de métricas disponíveis para um serviço.

**Método**: GET

**Pedido URI**: https: \/ \/ management.azure.com/subscriptions/*{subscriptionId}**/resourceGroups/ {resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}* / *{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por exemplo, para recuperar as definições métricas de uma conta de Armazenamento Azure, o pedido apareceria da seguinte forma:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Para obter definições métricas utilizando as métricas multidimensionais do Monitor Azure REST, utilize "2018-01-01" como versão API.
>
>

O corpo de resposta JSON resultante seria semelhante ao seguinte exemplo: (Note que a segunda métrica tem dimensões)

```json
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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Valores de dimensão de recuperação (API multidimensional)

Uma vez conhecidas as definições métricas disponíveis, pode haver algumas métricas que têm dimensões. Antes de consultar a métrica, poderá querer descobrir qual o alcance dos valores que uma dimensão tem. Com base nestes valores de dimensão, pode então optar por filtrar ou segmentar as métricas com base em valores de dimensão, enquanto se consulta métricas.  Utilize as [métricas do monitor Azure REST API](/rest/api/monitor/metrics) para o conseguir.

Utilize o nome da métrica 'valor' (não o '5º') para eventuais pedidos de filtragem . Se não forem especificados filtros, a métrica predefinida é devolvida. O uso desta API só permite que uma dimensão tenha um filtro wildcard.

> [!NOTE]
> Para recuperar valores de dimensão utilizando a API do Monitor Azure, utilize "2018-01-01" como versão API.
>
>

**Método**: GET

**Pedido URI**: https \: //management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-name}*/providers/*{resource-provider-namespace}* / *{resource-type}* / *{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&timepan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadados&versão api=*{apiVersion}*

Por exemplo, para recuperar a lista de valores de dimensão emitidos para a dimensão 'API Name' para a métrica 'Transacções', onde a dimensão GeoType = 'Primária' durante o intervalo de tempo especificado, o pedido seria o seguinte:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

O organismo de resposta JSON resultante seria semelhante ao seguinte exemplo:

```json
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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Valores métricos de recuperação (API multidimensional)

Uma vez conhecidas as definições métricas métricas disponíveis e os valores de dimensão possíveis, é então possível recuperar os valores métricos relacionados.  Utilize as [métricas do monitor Azure REST API](/rest/api/monitor/metrics) para o conseguir.

Utilize o nome da métrica 'valor' (não o '5º') para quaisquer pedidos de filtragem. Se não forem especificados filtros de dimensão, a métrica agregada enrolada é devolvida. Se uma consulta métrica retornar várias vezes, então pode usar os parâmetros de consulta 'Top' e 'OrderBy' para devolver uma lista limitada de timeeries encomendados.

> [!NOTE]
> Para recuperar valores métricos multidimensionais utilizando a API do Monitor Azure, utilize "2018-01-01" como versão API.
>
>

**Método**: GET

**Pedido URI**: https: \/ /management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-name}*/providers/*{resource-provider-namespace}* / *{resource-type}* / *{resource-name}*/providers/microsoft.insights/metricnames =*{métrica}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&intervalo=*{timeGrain}*&agregação=*{aggreation}*&versão api=*{apiVersion}*

Por exemplo, para recuperar as 3 APIs de topo, em valor descendente, pelo número de 'Transacções' durante uma gama de 5 min, onde o GeotType era 'Primário', o pedido seria o seguinte:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

O organismo de resposta JSON resultante seria semelhante ao seguinte exemplo:

```json
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

## <a name="retrieve-metric-definitions"></a>Recuperar definições métricas

Utilize as [definições Azure Monitor Metric REST API](/rest/api/monitor/metricdefinitions) para aceder à lista de métricas disponíveis para um serviço.

**Método**: GET

**Pedido URI**: https: \/ \/ management.azure.com/subscriptions/*{subscriptionId}**/resourceGroups/ {resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}* / *{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por exemplo, para recuperar as definições métricas de uma App Azure Logic, o pedido apareceria da seguinte forma:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Para obter definições métricas utilizando a API do Monitor Azure, utilize "2016-03-01" como versão API.
>
>

O organismo de resposta JSON resultante seria semelhante ao seguinte exemplo:

```json
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

Para obter mais informações, consulte a [Lista as definições métricas para um recurso na documentação AZure Monitor REST API.](/rest/api/monitor/metricdefinitions)

## <a name="retrieve-metric-values"></a>Recuperar valores métricos

Uma vez conhecidas as definições métricas disponíveis, é então possível recuperar os valores métricos relacionados. Utilize o nome da métrica 'valor' (não o 'utilizadores localizados') para quaisquer pedidos de filtragem (por exemplo, recuperar os pontos de dados métricos 'CpuTime' e 'Requests'). Se não forem especificados filtros, a métrica predefinida é devolvida.

> [!NOTE]
> Para recuperar valores métricos utilizando a API do Monitor Azure, utilize "2016-09-01" como versão API.
>
>

**Método:**`GET`

**Pedido URI:**`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}/providers/microsoft.insights/metrics?$filter={filter}&api-version={apiVersion}`

Por exemplo, para recuperar os pontos de dados métricos RunsSucceeded para o intervalo de tempo determinado e por um grão de tempo de 1 hora, o pedido seria o seguinte:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

O organismo de resposta JSON resultante seria semelhante ao seguinte exemplo:

```json
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

Para obter vários dados ou pontos de agregação, adicione os nomes de definição métrica e os tipos de agregação ao filtro, como se pode ver no exemplo seguinte:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

O organismo de resposta JSON resultante seria semelhante ao seguinte exemplo:

```json
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

Uma abordagem adicional é utilizar [o ARMClient](https://github.com/projectkudu/armclient) na sua máquina Windows. A ARMClient lida automaticamente com a autenticação AD AZure (e o token JWT resultante). Os seguintes passos descrevem a utilização do ARMClient para a recuperação de dados métricos:

1. Instale [chocolate e](https://chocolatey.org/) [ARMClient](https://github.com/projectkudu/armclient).
2. Numa janela de terminal, faça *armclient.exe login.* Fazê-lo leva-o a fazer login no Azure.
3. Tipo *de braço -your_resource_id]/fornecedores/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Tipo *de braços GET [your_resource_id]/fornecedores/microsoft.insights/metrics?api-version=2016-09-01*

Por exemplo, para recuperar as definições métricas para uma aplicação lógica específica, emita o seguinte comando:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Recuperar o ID do recurso

A utilização da API REST pode realmente ajudar a compreender as definições métricas métricas disponíveis, granularidade e valores conexos. Esta informação é útil quando se utiliza a [Biblioteca de Gestão Azure.](/previous-versions/azure/reference/mt417623(v=azure.100))

Para o código anterior, o ID de recurso a utilizar é o caminho completo para o recurso Azure desejado. Por exemplo, para consultar uma App Web Azure, o ID de recurso seria:

*/subscrições/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista a seguir contém alguns exemplos de formatos de ID de recursos para vários recursos Azure:

* **IoT Hub** - /subscrições/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Piscina ELÁSTICA SQL** - /subscrições/*{subscrição-id}*/resourceGroups/*{resource-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscrições/*{subscription-id}*/resourceGroups/*{resource-name}*/providers/Microsoft.Sql/servers/*{server-name}*/bases de dados/*{data-nome de base de dados}*
* **Service Bus** - /subscrições/*{subscription-id}*/resourceGroups/*{resource-name}*/providers/Microsoft.ServiceBus/*{namespace}* / *{servicebus-name}*
* **Conjuntos de escala de máquina virtual** - /subscrições/*{subscrição-id}*/resourceGroups/*{resource-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VMs** - /subscrições/*{subscrição-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Centros de Eventos** - /subscrições/*{subscrição-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Existem abordagens alternativas para recuperar o ID de recursos, incluindo a utilização do Azure Resource Explorer, visualização do recurso pretendido no portal Azure, e através do PowerShell ou do Azure CLI.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure

Para encontrar o ID de recurso para um recurso desejado, uma abordagem útil é utilizar a ferramenta [Azure Resource Explorer.](https://resources.azure.com) Navegue para o recurso pretendido e, em seguida, olhe para o ID mostrado, como na seguinte imagem:

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure

O ID de recursos também pode ser obtido a partir do portal Azure. Para tal, navegue para o recurso pretendido e, em seguida, selecione Propriedades. O ID de recursos é apresentado na secção Propriedades, como visto na seguinte imagem:

![Alt "ID de recurso exibido na lâmina propriedades no portal Azure"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

O ID do recurso também pode ser recuperado utilizando cmdlets Azure PowerShell. Por exemplo, para obter o ID de recurso para uma App Azure Logic, execute o Get-AzureLogicApp cmdlet, como no exemplo seguinte:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

O resultado deve ser semelhante ao seguinte exemplo:

```output
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

Para recuperar o ID de recurso para uma conta de Armazenamento Azure utilizando o Azure CLI, execute o `az storage account show` comando, como mostra o seguinte exemplo:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

O resultado deve ser semelhante ao seguinte exemplo:

```json
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
> As Azure Logic Apps ainda não estão disponíveis através do Azure CLI, pelo que uma conta de Armazenamento Azure é mostrada no exemplo anterior.
>
>

## <a name="retrieve-activity-log-data"></a>Recuperar dados de registo de atividade

Além das definições métricas e valores conexos, também é possível utilizar a Azure Monitor REST API para recuperar informações interessantes adicionais relacionadas com os recursos do Azure. Como exemplo, é possível consultar dados [de registo de atividade.](/rest/api/monitor/activitylogs) Os seguintes pedidos de amostra utilizam a Azure Monitor REST API para consultar o registo de atividade.

Obtenha registos de atividade com filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obtenha Registos de Atividade com filtro e selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenha registos de atividade com a seleção:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenha Registos de Atividade sem filtro ou selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="next-steps"></a>Passos seguintes

* Reveja a [visão geral da monitorização.](../overview.md)
* Ver as [métricas suportadas com o Monitor Azure](./metrics-supported.md).
* Reveja a [referência API do Microsoft Azure Monitor REST](/rest/api/monitor/).
* Reveja a [Biblioteca de Gestão Azure.](/previous-versions/azure/reference/mt417623(v=azure.100))