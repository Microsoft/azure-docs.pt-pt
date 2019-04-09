---
title: Automatizar a implementação de recursos para uma aplicação de função nas funções do Azure | Documentos da Microsoft
description: Saiba como criar um modelo do Azure Resource Manager que implementa a aplicação de funções.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure funções, funções, arquitetura sem servidor, infraestrutura como Gestor de recursos de código, do azure
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270908"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implementação de recursos para a sua aplicação de função nas funções do Azure

Pode utilizar um modelo Azure Resource Manager para implementar uma aplicação de funções. Este artigo descreve os recursos necessários e os parâmetros para fazer isso. Poderá ter de implementar recursos adicionais, dependendo do [acionadores e enlaces](functions-triggers-bindings.md) na sua aplicação de função.

Para obter mais informações sobre a criação de modelos, consulte [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para modelos de exemplo, consulte:
- [Aplicação de funções no plano de consumo]
- [Aplicação de funções num plano do serviço de aplicações do Azure]

> [!NOTE]
> O plano Premium para o alojamento das funções do Azure está atualmente em pré-visualização. Para obter mais informações, consulte [plano Premium de funções do Azure](functions-premium-plan.md).

## <a name="required-resources"></a>Recursos necessários

Uma implementação de funções do Azure inclui, normalmente, estes recursos:

| Recurso                                                                           | Requisito | Referência de sintaxe e as propriedades                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Uma aplicação de funções                                                                     | Necessário    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| Uma [armazenamento do Azure](../storage/index.yml) conta                                   | Necessário    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| Uma [Application Insights](../azure-monitor/app/app-insights-overview.md) componente | Opcional    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A [plano de alojamento](./functions-scale.md)                                             | Opcional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>um plano de alojamento só é necessário quando optar por executar a aplicação de funções num [plano Premium](./functions-premium-plan.md) (em pré-visualização) ou numa [plano do App Service](../app-service/overview-hosting-plans.md).

> [!TIP]
> Embora não seja necessário, é vivamente recomendado que configurar o Application Insights para a sua aplicação.

<a name="storage"></a>
### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para uma aplicação de funções. Precisa de uma conta de fins gerais que suporta blobs, tabelas, filas e ficheiros. Para obter mais informações, consulte [requisitos de conta de armazenamento das funções do Azure](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Além disso, a propriedade `AzureWebJobsStorage` deve ser especificado como uma definição de aplicação na configuração do site. Se a aplicação de funções não utiliza o Application Insights para monitorização, ele deve também especificar `AzureWebJobsDashboard` como uma definição de aplicação.

O runtime das funções do Azure utiliza o `AzureWebJobsStorage` cadeia de ligação para criar filas internas.  Quando o Application Insights não está ativado, o tempo de execução utiliza a `AzureWebJobsDashboard` cadeia de ligação para iniciar sessão para o armazenamento de tabelas do Azure e power a **Monitor** separador no portal do.

Estas propriedades especificadas a `appSettings` coleção no `siteConfig` objeto:

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

### <a name="application-insights"></a>Application Insights

Para monitorizar as suas aplicações de funções, recomenda-se o Application Insights. O recurso do Application Insights está definido com o tipo **Microsoft.Insights/components** e o tipo **web**:

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
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Além disso, a chave de instrumentação tem de ser fornecido para a aplicação de função com o `APPINSIGHTS_INSTRUMENTATIONKEY` definição da aplicação. Esta propriedade é especificada na `appSettings` coleção no `siteConfig` objeto:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plano de alojamento

A definição do plano de alojamento varia e pode ser um dos seguintes procedimentos:
* [Plano de consumo](#consumption) (predefinição)
* [O plano premium](#premium) (em pré-visualização)
* [Plano do Serviço de Aplicações](#app-service-plan)

### <a name="function-app"></a>Function App

O recurso de aplicação de função é definido usando um recurso do tipo **Microsoft.Web/sites** e o tipo **functionapp**:

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
```

> [!IMPORTANT]
> Se está explicitamente definindo um plano de alojamento, seria necessário um item adicional na matriz dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Uma aplicação de funções tem de incluir essas configurações de aplicativo:

| Nome da definição                 | Descrição                                                                               | Valores de exemplo                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Uma cadeia de ligação a um armazenamento de conta de que o runtime das funções de colocação em fila interna | Consulte [conta de armazenamento](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | A versão do runtime das funções do Azure                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | A pilha de linguagem a ser utilizado para funções nesta aplicação                                   | `dotnet`, `node`, `java`, ou `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Apenas necessário se utilizar o `node` pilha de linguagem, especifica a versão a utilizar              | `10.14.1`                             |

Estas propriedades especificadas a `appSettings` coleção no `siteConfig` propriedade:

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

## <a name="deploy-on-consumption-plan"></a>Implementar no plano de consumo

O plano de consumo aloca automaticamente a potência de computação quando seu código está em execução, aumenta horizontalmente conforme necessário para processar a carga e, em seguida, aumenta diminui quando o código não está em execução. Não é necessário pagar as VMs Inativas e não tiver a capacidade de reserva com antecedência. Para obter mais informações, consulte [as funções do Azure dimensionamento e alojamento](functions-scale.md#consumption-plan).

Para um modelo do Azure Resource Manager de exemplo, consulte [aplicação de funções no plano de consumo].

### <a name="create-a-consumption-plan"></a>Criar um plano de consumo

Plano de consumo não precisa ser definido. Um será automaticamente criado ou selecionado numa base por região, quando criar o recurso de aplicação de função em si.

O plano de consumo é um tipo especial de recurso de "farm de servidores". Para Windows, pode especificá-lo utilizando o `Dynamic` o valor para o `computeMode` e `sku` propriedades:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

> [!NOTE]
> O plano de consumo não é possível definir explicitamente para Linux. Esta será criada automaticamente.

Se definir explicitamente o seu plano de consumo, terá de definir o `serverFarmId` propriedade na aplicação, de modo que ele aponta para o ID de recurso do plano. Deve garantir que a aplicação de função tem um `dependsOn` definição para o plano também.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

#### <a name="windows"></a>Windows

No Windows, o plano de consumo requer duas definições adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Estas propriedades de configurar o caminho de ficheiro e da conta de armazenamento onde o código de aplicação de função e a configuração são armazenadas.

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

No Linux, a aplicação de funções tem de ter sua `kind` definido como `functionapp,linux`, e tem de ter o `reserved` definida como `true`:

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

## <a name="deploy-on-premium-plan"></a>Implementar o plano Premium

O plano Premium oferece o mesmo dimensionamento como o plano de consumo, mas inclui recursos dedicados e capacidades adicionais. Para obter mais informações, consulte [plano do Azure funções Premium (pré-visualização)](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Criar um plano Premium

Um plano Premium é um tipo especial de recurso de "farm de servidores". Pode especificá-lo através de um `EP1`, `EP2`, ou `EP3` para o `sku` valor da propriedade.

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

Uma aplicação de funções num plano Premium tem de ter o `serverFarmId` propriedade definida para o ID de recurso do plano que criou anteriormente. Além disso, um plano Premium requer duas definições adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Estas propriedades de configurar o caminho de ficheiro e da conta de armazenamento onde o código de aplicação de função e a configuração são armazenadas.

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

## <a name="deploy-on-app-service-plan"></a>Implementar o plano do serviço de aplicações

No plano do serviço de aplicações, a aplicação function app é executada em VMs dedicadas no básico, Standard e Premium SKUs, semelhante aos aplicativos web. Para obter detalhes sobre como funciona o plano do serviço de aplicações, consulte a [descrição geral aprofundada dos planos do App Service do Azure](../app-service/overview-hosting-plans.md).

Para um modelo do Azure Resource Manager de exemplo, consulte [aplicação de funções num plano do serviço de aplicações do Azure].

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um plano do serviço de aplicações é definido por um recurso de "farm de servidores".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

Para executar a aplicação no Linux, também tem de definir o `kind` para `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Criar uma aplicação de função 

Uma aplicação de funções num plano do serviço de aplicações tem de ter o `serverFarmId` propriedade definida para o ID de recurso do plano que criou anteriormente.

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

Aplicações do Linux também devem incluir uma `linuxFxVersion` propriedade em `siteConfig`. Se estiver a implementar apenas código, o valor para que isso é determinado pela sua pilha de tempo de execução desejado:

| Pilha            | Valor de exemplo                                         |
|------------------|-------------------------------------------------------|
| Python (Pré-visualização) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
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

Se estiver [implantação de uma imagem de contentor personalizado](./functions-create-function-linux-custom-image.md), tem de especificar com `linuxFxVersion` e incluir a configuração permite que sua imagem ser solicitada, como em [aplicação Web para contentores](/azure/app-service/containers). Além disso, defina `WEBSITES_ENABLE_APP_SERVICE_STORAGE` para `false`, uma vez que o conteúdo da sua aplicação é fornecido no próprio contêiner:

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

## <a name="customizing-a-deployment"></a>Personalizar uma implantação

Uma aplicação de funções tem muitos recursos filho que podem ser utilizados na sua implementação, incluindo as definições da aplicação e opções de controle de origem. Também pode optar por remover o **sourcecontrols** recurso subordinado e utilize outro [opção de implementação](functions-continuous-deployment.md) em vez disso.

> [!IMPORTANT]
> Para implementar com êxito a sua aplicação através da utilização do Azure Resource Manager, é importante compreender a forma como os recursos são implementados no Azure. No exemplo seguinte, as configurações de nível superior são aplicadas utilizando **siteConfig**. É importante definir essas configurações num nível superior, porque eles veicular informações para o mecanismo de tempo de execução e implementação de funções. São necessárias informações de nível superior antes do filho **sourcecontrols/web** recurso é aplicado. Embora seja possível configurar estas definições no nível subordinado **config/appSettings** recurso, em alguns casos, a aplicação de função tem de ser implementada *antes* **config/appSettings**  é aplicada. Por exemplo, quando estiver a utilizar funções com [Logic Apps](../logic-apps/index.yml), as suas funções são uma dependência de outro recurso.

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
> Este modelo utiliza a [projeto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) valor de definições de aplicação, que define o diretório base no qual o motor de implementação de funções (Kudu) procura por código implantável. No nosso repositório, as nossas funções estão numa subpasta dos **src** pasta. Por isso, no exemplo anterior, definimos o valor de definições de aplicação como `src`. Se as suas funções estão na raiz do repositório, ou se não estiver a implementar no controle da fonte, pode remover este valor de definições de aplicação.

## <a name="deploy-your-template"></a>Implementar o modelo

Pode usar qualquer uma das seguintes formas para implementar o modelo:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Implementar no botão do Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` com um [com codificação URL](https://www.bing.com/search?q=url+encode) versão do caminho não processado de sua `azuredeploy.json` ficheiro no GitHub.

Eis um exemplo que usa o markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Eis um exemplo que usa o HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como desenvolver e configurar as funções do Azure.

* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Como configurar as definições de aplicação de função do Azure](functions-how-to-use-azure-function-app-settings.md)
* [Criar a sua primeira função do Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplicação de funções no plano de consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplicação de funções num plano do serviço de aplicações do Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
