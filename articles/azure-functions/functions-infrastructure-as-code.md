---
title: Automatizar a implantação de recursos do aplicativo de funções no Azure
description: Saiba como criar um modelo de Azure Resource Manager que implanta seu aplicativo de funções.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: bb2371fc7732e8fa6fcfea53bf2822fcf3d7d2fa
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963959"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implantação de recursos para seu aplicativo de funções no Azure Functions

Você pode usar um modelo de Azure Resource Manager para implantar um aplicativo de funções. Este artigo descreve os recursos e os parâmetros necessários para fazer isso. Talvez seja necessário implantar recursos adicionais, dependendo dos [gatilhos e das associações](functions-triggers-bindings.md) em seu aplicativo de funções.

Para obter mais informações sobre como criar modelos, consulte Criando [modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Para modelos de exemplo, consulte:
- [Aplicativo de funções no plano de consumo]
- [Aplicativo de funções no plano de serviço Azure App]

## <a name="required-resources"></a>Recursos necessários

Uma implantação Azure Functions normalmente consiste nesses recursos:

| Recurso                                                                           | Requisito | Referência de sintaxe e propriedades                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Um aplicativo de funções                                                                     | Obrigatório    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| Uma conta de [armazenamento do Azure](../storage/index.yml)                                   | Obrigatório    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| Um componente [Application insights](../azure-monitor/app/app-insights-overview.md) | Opcional    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| Um [plano de hospedagem](./functions-scale.md)                                             | Opcional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup> Um plano de hospedagem só é necessário quando você opta por executar seu aplicativo de funções em um [plano Premium](./functions-premium-plan.md) (em versão prévia) ou em um [plano do serviço de aplicativo](../app-service/overview-hosting-plans.md).

> [!TIP]
> Embora não seja necessário, é altamente recomendável que você configure Application Insights para seu aplicativo.

<a name="storage"></a>
### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para um aplicativo de funções. Você precisa de uma conta de uso geral que dê suporte a BLOBs, tabelas, filas e arquivos. Para obter mais informações, consulte [Azure Functions requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Além disso, a propriedade `AzureWebJobsStorage` deve ser especificada como uma configuração de aplicativo na configuração do site. Se o aplicativo de funções não usar Application Insights para monitoramento, ele também deverá especificar `AzureWebJobsDashboard` como uma configuração de aplicativo.

O tempo de execução de Azure Functions usa a cadeia de conexão `AzureWebJobsStorage` para criar filas internas.  Quando Application Insights não está habilitado, o tempo de execução usa a cadeia de conexão `AzureWebJobsDashboard` para fazer logon no armazenamento de tabelas do Azure e ligar a guia **Monitor** no Portal.

Essas propriedades são especificadas na coleção de `appSettings` no objeto `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Estatísticas das Aplicações

Application Insights é recomendado para monitorar seus aplicativos de funções. O recurso Application Insights é definido com o tipo **Microsoft. insights/Components** e o tipo **Web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Além disso, a chave de instrumentação deve ser fornecida na aplicação de funções utilizando a definição de aplicação `APPINSIGHTS_INSTRUMENTATIONKEY`. Essa propriedade é especificada na coleção de `appSettings` no objeto `siteConfig`:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plano de hospedagem

A definição do plano de hospedagem varia e pode ser uma das seguintes:
* [Plano de consumo](#consumption) (padrão)
* [Plano Premium](#premium) (em versão prévia)
* [Plano do Serviço de Aplicações](#app-service-plan)

### <a name="function-app"></a>Function app

O recurso de aplicativo de funções é definido usando um recurso do tipo **Microsoft. Web/sites e o** tipo **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Se estiver a definir explicitamente um plano de hospedagem, será necessário um item adicional na matriz dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Um aplicativo de funções deve incluir estas configurações de aplicativo:

| Nome da definição                 | Descrição                                                                               | Valores de exemplo                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Uma cadeia de conexão para uma conta de armazenamento que o tempo de execução do Functions usa para a fila interna | Consulte a [conta de armazenamento](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | A versão do Azure Functions Runtime                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | A pilha de idiomas a ser usada para funções neste aplicativo                                   | `dotnet`, `node`, `java`, `python`ou `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Necessário apenas se estiver usando a pilha de idiomas `node`, especifica a versão a ser usada              | `10.14.1`                             |

Essas propriedades são especificadas na coleção de `appSettings` na propriedade `siteConfig`:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Implantar no plano de consumo

O plano de consumo automaticamente aloca poder de computação quando seu código está em execução, dimensiona horizontalmente conforme necessário para lidar com a carga e, em seguida, é dimensionado quando o código não está em execução. Você não precisa pagar por VMs ociosas e não precisa reservar a capacidade com antecedência. Para saber mais, consulte [Azure Functions escala e hospedagem](functions-scale.md#consumption-plan).

Para obter um modelo de Azure Resource Manager de exemplo, consulte [aplicativo de funções no plano de consumo].

### <a name="create-a-consumption-plan"></a>Criar um plano de consumo

Um plano de consumo não precisa ser definido. Uma será criada automaticamente ou selecionada em uma base por região quando você criar o recurso do aplicativo de funções em si.

O plano de consumo é um tipo especial de recurso "ServerFarm". Para o Windows, você pode especificá-lo usando o valor `Dynamic` para as propriedades `computeMode` e `sku`:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> O plano de consumo não pode ser explicitamente definido para Linux. Ele será criado automaticamente.

Se você definir explicitamente seu plano de consumo, será necessário definir a propriedade `serverFarmId` no aplicativo para que ele aponte para a ID de recurso do plano. Você deve garantir que o aplicativo de funções também tenha uma configuração `dependsOn` para o plano.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

#### <a name="windows"></a>Windows

No Windows, um plano de consumo requer duas definições adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Essas propriedades configuram a conta de armazenamento e o caminho do arquivo onde o código do aplicativo de funções e a configuração são armazenados.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

No Linux, o aplicativo de funções deve ter seu `kind` definido como `functionapp,linux`e deve ter a propriedade `reserved` definida como `true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Implantar no plano Premium

O plano Premium oferece o mesmo dimensionamento do plano de consumo, mas inclui recursos dedicados e recursos adicionais. Para saber mais, confira [Azure Functions plano Premium](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Criar um plano Premium

Um plano Premium é um tipo especial de recurso "ServerFarm". Você pode especificá-lo usando `EP1`, `EP2`ou `EP3` para o valor da propriedade `sku`.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Criar uma aplicação de função

Um aplicativo de funções em um plano Premium deve ter a propriedade `serverFarmId` definida como a ID de recurso do plano criado anteriormente. Além disso, um plano Premium requer duas configurações adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Essas propriedades configuram a conta de armazenamento e o caminho do arquivo onde o código do aplicativo de funções e a configuração são armazenados.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Implantar no plano do serviço de aplicativo

No plano do serviço de aplicativo, seu aplicativo de funções é executado em VMs dedicadas em SKUs Basic, Standard e Premium, semelhante aos aplicativos Web. Para obter detalhes sobre como o plano do serviço de aplicativo funciona, consulte a [visão geral detalhada dos planos de serviço Azure app](../app-service/overview-hosting-plans.md).

Para obter um modelo de Azure Resource Manager de exemplo, consulte [aplicativo de funções no plano de serviço Azure app].

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um plano do serviço de aplicativo é definido por um recurso "ServerFarm".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Para executar seu aplicativo no Linux, você também deve definir o `kind` como `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Criar uma aplicação de função

Um aplicativo de funções em um plano do serviço de aplicativo deve ter a propriedade `serverFarmId` definida como a ID de recurso do plano criado anteriormente.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Os aplicativos do Linux também devem incluir uma propriedade `linuxFxVersion` em `siteConfig`. Se você estiver apenas implantando o código, o valor para isso será determinado pela pilha de tempo de execução desejada:

| Pilha            | Valor de exemplo                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| Javascript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Se você estiver [implantando uma imagem de contêiner personalizada](./functions-create-function-linux-custom-image.md), deverá especificá-la com `linuxFxVersion` e incluir a configuração que permite que a imagem seja puxada, como em [aplicativo Web para contêineres](/azure/app-service/containers). Além disso, detete `WEBSITES_ENABLE_APP_SERVICE_STORAGE` para `false`, uma vez que o conteúdo da sua aplicação é fornecido no próprio recipiente:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Personalizando uma implantação

Um aplicativo de funções tem muitos recursos filho que você pode usar em sua implantação, incluindo configurações de aplicativo e opções de controle do código-fonte. Você também pode optar por remover o recurso filho **sourcecontrols** e usar uma opção de [implantação](functions-continuous-deployment.md) diferente.

> [!IMPORTANT]
> Para implantar o aplicativo com êxito usando Azure Resource Manager, é importante entender como os recursos são implantados no Azure. No exemplo a seguir, as configurações de nível superior são aplicadas usando **siteConfig**. É importante definir essas configurações em um nível superior, pois elas transmitem informações para o tempo de execução e o mecanismo de implantação do functions. As informações de nível superior são necessárias antes que o recurso **sourcecontrols/Web** filho seja aplicado. Embora seja possível definir essas configurações no recurso de **config/appSettings** de nível filho, em alguns casos, seu aplicativo de funções deve ser implantado *antes que* **config/appSettings** seja aplicado. Por exemplo, quando você estiver usando funções com [aplicativos lógicos](../logic-apps/index.yml), suas funções serão uma dependência de outro recurso.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Este modelo usa o valor configurações do aplicativo do [projeto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) , que define o diretório base no qual o mecanismo de implantação de funções (kudu) procura o código implantável. Em nosso repositório, nossas funções estão em uma subpasta da pasta **src** . Portanto, no exemplo anterior, definimos o valor de configurações do aplicativo como `src`. Se suas funções estiverem na raiz do repositório ou se você não estiver implantando do controle do código-fonte, você poderá remover esse valor de configurações do aplicativo.

## <a name="deploy-your-template"></a>Implementar o modelo

Você pode usar qualquer uma das seguintes maneiras para implantar seu modelo:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
* [API REST](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botão implantar no Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` por uma versão [codificada por URL](https://www.bing.com/search?q=url+encode) do caminho bruto do arquivo de `azuredeploy.json` no github.

Veja um exemplo que usa a redução:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Aqui está um exemplo que usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implementar com o PowerShell

Os comandos do PowerShell a seguir criam um grupo de recursos e implantam um modelo que cria um aplicativo de funções com seus recursos necessários. Para executar localmente, você deve ter o [Azure PowerShell](/powershell/azure/install-az-ps) instalado. Execute [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) para entrar.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Para testar essa implantação, você pode usar um [modelo como este](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) que cria um aplicativo de funções no Windows em um plano de consumo. Substitua `<function-app-name>` por um nome exclusivo para seu aplicativo de funções.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como desenvolver e configurar Azure Functions.

* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Como definir as configurações do aplicativo de funções do Azure](functions-how-to-use-azure-function-app-settings.md)
* [Criar sua primeira função do Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplicativo de funções no plano de consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplicativo de funções no plano de serviço Azure App]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
