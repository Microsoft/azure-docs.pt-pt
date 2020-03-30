---
title: Passagem da API de Monitorização Azure
description: Como autenticar pedidos e utilizar a API DO MONITOR Azure para recuperar as definições métricas disponíveis e os valores métricos.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.openlocfilehash: f52776fc6b5f5f530cc368a2f148a2ff63fb5b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294628"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Passagem da API de Monitorização Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra-lhe como executar a autenticação para que o seu código possa utilizar a referência rest API do [Microsoft Azure Monitor .](https://docs.microsoft.com/rest/api/monitor/)

A API Do Monitor Azure permite recuperar programáticamente as definições métricas padrão disponíveis, granularidade e valores métricos. Os dados podem ser guardados numa loja de dados separada, como a Base de Dados Azure SQL, o Azure Cosmos DB ou o Lago de Dados Azure. A partir daí, uma análise adicional pode ser feita conforme necessário.

Além de trabalhar com vários pontos de dados métricos, a API monitor além de permitir listar regras de alerta, ver registos de atividades e muito mais. Para obter uma lista completa das operações disponíveis, consulte a referência REST API do [Microsoft Azure Monitor .](https://docs.microsoft.com/rest/api/monitor/)

## <a name="authenticating-azure-monitor-requests"></a>Autenticação de pedidos do Monitor Azure

O primeiro passo é autenticar o pedido.

Todas as tarefas executadas contra a API do Monitor Azure utilizam o modelo de autenticação do Gestor de Recursos Azure. Por isso, todos os pedidos devem ser autenticados com o Azure Ative Directory (Azure AD). Uma abordagem para autenticar a aplicação do cliente é criar um diretor de serviço Azure AD e recuperar o símbolo de autenticação (JWT). O seguinte script de amostra demonstra a criação de um serviço Azure AD principal via PowerShell. Para uma passagem mais detalhada, consulte a documentação sobre a utilização do [Azure PowerShell para criar um serviço principal para aceder aos recursos.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps) Também é possível [criar um serviço principal através do portal Azure.](../../active-directory/develop/howto-create-service-principal-portal.md)

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

Para consultar a API do Monitor Azure, a aplicação do cliente deve utilizar o principal de serviço previamente criado para autenticar. O seguinte exemplo o script PowerShell mostra uma abordagem, utilizando a Biblioteca de [Autenticação do Diretório Ativo](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) para obter o símbolo de autenticação JWT. O símbolo JWT é passado como parte de um parâmetro de autorização http em pedidos à API DO MONITOR Azure REST.

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

Após a autenticação, as consultas podem então ser executadas contra a API REST Do Monitor Azure. Há duas consultas úteis:

1. Enumerar as definições métricas para um recurso
2. Recuperar os valores métricos

> [!NOTE]
> Para obter informações adicionais sobre a autenticação com a API Azure REST, consulte a [Referência API Do REST Azure](https://docs.microsoft.com/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Recuperar Definições Métricas (API Multidimensional)

Utilize as [definições métricas do Monitor Azure REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) para aceder à lista de métricas disponíveis para um serviço.

**Método**: GET

**Pedido URI**:\/\/https: management.azure.com/subscriptions/*{subscriçãoId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

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
> Para recuperar definições métricas utilizando as métricas multidimensionais do Monitor Azure REST API, utilize "2018-01-01" como versão API.
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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Recuperar Valores de Dimensão (API multidimensional)

Uma vez conhecidas as definições métricas disponíveis, pode haver algumas métricas que têm dimensões. Antes de consultar a métrica, pode querer descobrir qual a gama de valores que uma dimensão tem. Com base nestes valores de dimensão pode então optar por filtrar ou segmentar as métricas com base nos valores de dimensão enquanto se consulta míseros.  Utilize a API de Métricas de [Monitor Azure PARA](https://docs.microsoft.com/rest/api/monitor/metrics) o conseguir.

Utilize o nome 'valor' da métrica (não o 'valor localizado') para quaisquer pedidos de filtragem . Se não forem especificados filtros, a métrica padrão é devolvida. O uso desta API só permite que uma dimensão tenha um filtro wildcard.

> [!NOTE]
> Para recuperar valores de dimensão utilizando a API DO MONITOR Azure, utilize "2018-01-01" como versão API.
>
>

**Método**: GET

**Pedido URI**\:: https //management.azure.com/subscriptions/*{subscrição-id}*/recursosGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type} {resource-name}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadados&*api-version= {apiVersion }*

Por exemplo, para recuperar a lista dos valores de dimensão emitidos para a métrica 'Denominação API' para a métrica 'Transacções', em que a dimensão GeoType = 'Primária' durante o intervalo de tempo especificado, o pedido seria o seguinte:

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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Recuperar Valores Métricos (API multidimensional)

Uma vez conhecidas as definições métricas disponíveis e os possíveis valores de dimensão, é então possível recuperar os valores métricos relacionados.  Utilize a API de Métricas de [Monitor Azure PARA](https://docs.microsoft.com/rest/api/monitor/metrics) o conseguir.

Utilize o nome 'valor' da métrica (não o 'valor localizado') para quaisquer pedidos de filtragem. Se não forem especificados filtros de dimensão, a métrica agregada enrolada é devolvida. Se uma consulta métrica devolver várias séries de tempo, então pode utilizar os parâmetros de consulta 'Top' e 'OrderBy' para devolver uma lista limitada de séries de tempo ordenadas.

> [!NOTE]
> Para recuperar valores métricos multidimensionais utilizando a API DO Monitor Azure, utilize "2018-01-01" como versão API.
>
>

**Método**: GET

**Pedido**URI https://management.azure.com/subscriptions/: *{subscrição-id}*/recursosGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type} {resource-name}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&timepan=*{starttime/endtime}*&$filter=*{filter}*&intervalo=*{timeGrain}*&aggregation=*{aggreation}*&api* apiVersion}*

Por exemplo, para recuperar as 3 MELHORES APIs, em valor descendente, pelo número de 'Transacções' durante uma gama de 5 min, onde o GeotType era 'Primário', o pedido seria o seguinte:

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

Utilize as [definições métricas do Monitor Azure REST API](https://msdn.microsoft.com/library/mt743621.aspx) para aceder à lista de métricas disponíveis para um serviço.

**Método**: GET

**Pedido URI**:\/\/https: management.azure.com/subscriptions/*{subscriçãoId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por exemplo, para recuperar as definições métricas de uma App Lógica Azure, o pedido apareceria da seguinte forma:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Para recuperar definições métricas utilizando a API DO Monitor Azure, utilize "2016-03-01" como versão API.
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

Para obter mais informações, consulte a Lista das definições métricas para um recurso na documentação [da API do Monitor Do TCAI.](https://msdn.microsoft.com/library/azure/mt743621.aspx)

## <a name="retrieve-metric-values"></a>Recuperar valores métricos

Uma vez conhecidas as definições métricas disponíveis, é então possível recuperar os valores métricos relacionados. Utilize o nome 'valor' da métrica (não o 'Valor localizado') para quaisquer pedidos de filtragem (por exemplo, recupere os pontos de dados métricos 'CpuTime' e 'Requests'). Se não forem especificados filtros, a métrica padrão é devolvida.

> [!NOTE]
> Para recuperar valores métricos utilizando a API DO Monitor Azure, utilize "2016-09-01" como versão API.
>
>

**Método**: GET

**Pedido URI:**`https://management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Por exemplo, para recuperar os pontos de dados métricos runsSucceed para o intervalo de tempo determinado e por um tempo grão de 1 hora, o pedido seria o seguinte:

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

Para recuperar vários dados ou pontos de agregação, adicione os nomes de definição métrica e os tipos de agregação ao filtro, como se pode ver no seguinte exemplo:

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

### <a name="use-armclient"></a>Utilizar o ARMClient

Uma abordagem adicional é usar o [ARMClient](https://github.com/projectkudu/armclient) na sua máquina Windows. ArmClient trata automaticamente da autenticação Azure AD (e consequente ficha JWT). Os seguintes passos descrevem a utilização do ARMClient para a recuperação de dados métricos:

1. Instale [Chocolatey](https://chocolatey.org/) e [ARMClient.](https://github.com/projectkudu/armclient)
2. Numa janela terminal, escreva *login armclient.exe*. Fazê-lo pede-lhe para iniciar sessão no Azure.
3. Tipo *armclient GET [your_resource_id]/fornecedores/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Tipo *armclient GET [your_resource_id]/fornecedores/microsoft.insights/metrics?api-version=2016-09-01*

Por exemplo, para recuperar as definições métricas de uma aplicação lógica específica, emita o seguinte comando:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Recuperar o ID do recurso

A utilização da API REST pode realmente ajudar a compreender as definições métricas disponíveis, granularidade e valores relacionados. Essa informação é útil na utilização da Biblioteca de [Gestão Azure.](https://msdn.microsoft.com/library/azure/mt417623.aspx)

Para o código anterior, o ID de recurso a utilizar é o caminho completo para o recurso Azure pretendido. Por exemplo, para consultar uma Web App Azure, o ID de recursos seria:

*/subscrições/{subscrição-id}/recursosGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista que se segue contém alguns exemplos de formatos de ID de recursos para vários recursos Azure:

* **IoT Hub** - /subscrições/*{subscrição-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Piscina SQL Elástica** - /subscrições/*{subscrição-id}*/recursosGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* Base de **Dados SQL (v12)** - /subscrições/*{subscrição-id}*/recursosGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/bases de dados/*{database-name}*
* **Service Bus** - /subscrições/*{subscrição-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Conjuntos** de escala de máquina virtual - /subscrições/*{subscrição-id}*/recursosGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VMs** - /subscrições/*{subscrição-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* Centros de **Eventos** - /subscrições/*{subscrição-id}*/recursosGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Existem abordagens alternativas para recuperar o ID de recursos, incluindo a utilização do Azure Resource Explorer, visualização do recurso desejado no portal Azure, e via PowerShell ou o Azure CLI.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure

Para encontrar o ID de recurso para um recurso desejado, uma abordagem útil é usar a ferramenta [Azure Resource Explorer.](https://resources.azure.com) Navegue para o recurso desejado e, em seguida, olhe para o ID mostrado, como na seguinte imagem:

![Alt "Explorador de Recursos Azure"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure

O ID de recurso também pode ser obtido a partir do portal Azure. Para isso, navegue para o recurso pretendido e, em seguida, selecione Propriedades. O ID de recurso é apresentado na secção Propriedades, como se pode ver na seguinte imagem:

![Alt "Id de recurso exibido na lâmina propriedades no portal Azure"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

O ID de recurso também pode ser recuperado utilizando cmdlets Azure PowerShell. Por exemplo, para obter o ID de recurso para uma App Lógica Azure, execute o cmdlet Get-AzureLogicApp, como no exemplo seguinte:

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

Para recuperar o ID de recurso para uma conta de `az storage account show` Armazenamento Azure utilizando o AZURE CLI, execute o comando, como se pode ver no seguinte exemplo:

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
> As Aplicações Lógicas Azure ainda não estão disponíveis através do Azure CLI, pelo que uma conta de Armazenamento Azure é mostrada no exemplo anterior.
>
>

## <a name="retrieve-activity-log-data"></a>Recuperar dados de registo de atividade

Além das definições métricas e valores conexos, também é possível utilizar a API DO MONITOR Azure para obter informações interessantes adicionais relacionadas com os recursos do Azure. Como exemplo, é possível consultar dados de registo de [atividade.](https://msdn.microsoft.com/library/azure/dn931934.aspx) A amostra seguinte demonstra a utilização da API DO REST do Monitor Azure para consultar dados de registo de atividade dentro de uma gama de data específica para uma subscrição do Azure:

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

* Reveja a [visão geral da monitorização](../../azure-monitor/overview.md).
* Ver as [métricas suportadas com o Monitor Azure](metrics-supported.md).
* Reveja a referência REST API do [Microsoft Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Reveja a Biblioteca de [Gestão Azure.](https://msdn.microsoft.com/library/azure/mt417623.aspx)
