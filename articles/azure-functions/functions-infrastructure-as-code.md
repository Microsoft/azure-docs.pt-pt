---
title: Automatizar a implementação de recursos de aplicativos de função para o Azure
description: Saiba como construir um modelo de Gestor de Recursos Azure que implementa a sua aplicação de função.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: a1b621b5d5601e6d8bffef48e23d217e0eee1d6a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725824"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implementação de recursos para a sua aplicação de funções em Funções Azure

Pode utilizar um modelo de Gestor de Recursos Azure para implementar uma aplicação de função. Este artigo descreve os recursos e parâmetros necessários para o fazer. Poderá ser necessário implementar recursos adicionais, dependendo dos [gatilhos e encadernações](functions-triggers-bindings.md) na sua aplicação de função.

Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md) (Criar modelos do Azure Resource Manager).

Para modelos de amostra, consulte:
- [App de função no plano de consumo]
- [App de função no plano de serviço de aplicações Azure]

## <a name="required-resources"></a>Recursos necessários

Uma implantação de funções Azure consiste tipicamente nestes recursos:

| Recurso                                                                           | Requisito | Referência de sintaxe e propriedades                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Um aplicativo de função                                                                     | Necessário    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| Uma conta [de armazenamento Azure](../storage/index.yml)                                   | Necessário    | [Microsoft.Storage/storageAcontas](/azure/templates/microsoft.storage/storageaccounts) |
| Um componente [de Insights de Aplicação](../azure-monitor/app/app-insights-overview.md) | Opcional    | [Microsoft.Insights/componentes](/azure/templates/microsoft.insights/components)         |
| Um [plano de hospedagem](./functions-scale.md)                                             | Opcional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Um plano de hospedagem só é necessário quando optar por executar a sua aplicação de função num [plano Premium](./functions-premium-plan.md) ou num plano de Serviço [de Aplicações.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Apesar de não ser necessário, recomenda-se vivamente que configuure os Insights de Aplicação para a sua aplicação.

<a name="storage"></a>
### <a name="storage-account"></a>Conta de armazenamento

É necessária uma conta de armazenamento Azure para uma aplicação de função. Precisa de uma conta de propósito geral que suporte bolhas, mesas, filas e ficheiros. Para obter mais informações, consulte [os requisitos da conta de armazenamento Azure Functions](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Além disso, a propriedade `AzureWebJobsStorage` deve ser especificada como uma configuração de aplicação na configuração do site. Se a aplicação de função não utilizar o Application Insights para monitorização, também deve especificar `AzureWebJobsDashboard` como uma configuração de aplicação.

O tempo de funcionamento das funções Azure utiliza o `AzureWebJobsStorage` fio de ligação para criar filas internas.  Quando o Application Insights não está ativado, o tempo de execução utiliza o `AzureWebJobsDashboard` fio de ligação para iniciar sessão no armazenamento da tabela Azure e alimenta o **separador Monitor** no portal.

Estas propriedades estão especificadas na `appSettings` coleção no `siteConfig` objeto:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

O Application Insights é recomendado para monitorizar as suas aplicações de função. O recurso Application Insights é definido com o tipo **Microsoft.Insights/componentes** e o tipo **web**:

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

Além disso, a chave de instrumentação deve ser fornecida à aplicação de função utilizando a definição da `APPINSIGHTS_INSTRUMENTATIONKEY` aplicação. Esta propriedade é especificada na `appSettings` coleção no `siteConfig` objeto:

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
* [Plano Premium](#premium)
* [Plano de Serviço de Aplicações](#app-service-plan)

### <a name="function-app"></a>Aplicação de funções

O recurso de aplicação de função é definido utilizando um recurso do tipo **Microsoft.Web/sites** e **do app de função** do tipo :

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
> Se estiver a definir explicitamente um plano de hospedagem, será necessário um item adicional na matriz dependOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Uma aplicação de função deve incluir estas definições de aplicação:

| Nome da definição                 | Descrição                                                                               | Valores de exemplo                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Uma cadeia de ligação a uma conta de armazenamento que o tempo de execução de Funções utiliza para a fila interna | Ver [conta de armazenamento](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | A versão do tempo de funcionamento das Funções Azure                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | A pilha de idiomas a ser usada para funções nesta aplicação                                   | `dotnet`, `node` `java` , `python` ou `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Só é necessário se utilizar a `node` pilha de idiomas, especifica a versão a utilizar              | `10.14.1`                             |

Estas propriedades estão especificadas na `appSettings` coleção do `siteConfig` imóvel:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Implantação no plano de consumo

O plano de consumo atribui automaticamente a potência de computação quando o seu código está em funcionamento, escala-se conforme necessário para manusear a carga e, em seguida, escala quando o código não está em funcionamento. Não tens de pagar por VMs ociosos, e não tens de reservar capacidade antecipadamente. Para saber mais, consulte [a escala e hospedagem de Funções Azure.](consumption-plan.md)

Para obter uma amostra do modelo do Gestor de Recursos Azure, consulte [a aplicação funções no plano de consumo.]

### <a name="create-a-consumption-plan"></a>Criar um plano de consumo

Um plano de consumo não precisa de ser definido. Um deles será automaticamente criado ou selecionado numa base por região quando criar o próprio recurso da aplicação de função.

O plano de consumo é um tipo especial de recurso "serverfarm". Para o Windows, pode especiá-lo utilizando o `Dynamic` valor para as `computeMode` `sku` propriedades:

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
> O plano de consumo não pode ser explicitamente definido para o Linux. Será criado automaticamente.

Se definir explicitamente o seu plano de Consumo, terá de definir o `serverFarmId` imóvel na aplicação para que este aponte para o ID de recursos do plano. Deve certificar-se de que a aplicação de função também tem uma `dependsOn` definição para o plano.

### <a name="create-a-function-app"></a>Criar uma aplicação de funções

As definições exigidas por uma aplicação de função em execução no plano de consumo adiam entre o Windows e o Linux. 

#### <a name="windows"></a>Windows

No Windows, um plano de consumo requer uma definição adicional na configuração do site: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Esta propriedade configura a conta de armazenamento onde o código e configuração da aplicação de função são armazenados.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Não defina a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) definição como é gerada para si quando o site for criado pela primeira vez.  

#### <a name="linux"></a>Linux

No Linux, a aplicação de função deve ter o seu `kind` conjunto para , e deve ter a propriedade definida para `functionapp,linux` `reserved` `true` . 

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

As [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) configurações e as configurações não são suportadas no Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Implementar no plano Premium

O plano Premium oferece a mesma escala que o plano de Consumo, mas inclui recursos dedicados e capacidades adicionais. Para saber mais, consulte [o Plano Premium Azure Functions.](./functions-premium-plan.md)

### <a name="create-a-premium-plan"></a>Criar um plano Premium

Um plano Premium é um tipo especial de recurso "serverfarm". Pode especiá-lo utilizando qualquer `EP1` um, `EP2` ou para o valor da propriedade no objeto de `EP3` `Name` `sku` [descrição.](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Criar uma aplicação de funções

Uma aplicação de função num plano Premium deve ter a `serverFarmId` propriedade definida para o ID de recursos do plano criado anteriormente. Além disso, um plano Premium requer uma configuração adicional na configuração do site: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Esta propriedade configura a conta de armazenamento onde o código e configuração da aplicação de função são armazenados.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Não defina a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) definição como é gerada para si quando o site for criado pela primeira vez.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Implementar no plano de Serviço de Aplicações

No plano de Serviço de Aplicações, a sua aplicação de função funciona em VMs dedicados em SKUs básicos, padrão e premium, semelhantes às aplicações web. Para mais detalhes sobre o funcionamento do plano do Serviço de Aplicações, consulte os planos do [Azure App Service em visão geral aprofundada.](../app-service/overview-hosting-plans.md)

Para obter uma amostra do modelo do Gestor de Recursos Azure, consulte [a aplicação Function no plano do Serviço de Aplicações Azure].

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um plano de Serviço de Aplicações é definido por um recurso "serverfarm".

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

Para executar a sua aplicação no Linux, também deve definir `kind` o `Linux` seguinte:

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

### <a name="create-a-function-app"></a>Criar uma aplicação de funções

Uma aplicação de função num plano de Serviço de Aplicações deve ter a `serverFarmId` propriedade definida para o ID de recursos do plano criado anteriormente.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

As aplicações Linux também devem incluir uma `linuxFxVersion` propriedade em `siteConfig` . Se estiver apenas a implementar código, o valor para tal é determinado pela sua stack de tempo de execução desejada no formato ```runtime|runtimeVersion``` de:

| Pilha            | Valor de exemplo                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.0` |

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

Se estiver [a implementar uma imagem personalizada do recipiente,](./functions-create-function-linux-custom-image.md)tem de especirá-la e incluir uma `linuxFxVersion` configuração que permita puxar a sua imagem, como na [Web App para Contentores.](../app-service/index.yml) Além disso, definido `WEBSITES_ENABLE_APP_SERVICE_STORAGE` para , uma vez que o seu conteúdo da `false` aplicação é fornecido no próprio recipiente:

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
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

Uma aplicação de função tem muitos recursos para crianças que pode usar na sua implementação, incluindo definições de aplicações e opções de controlo de fontes. Também pode optar por remover o recurso para **crianças controle de origem** e utilizar uma [opção de implementação](functions-continuous-deployment.md) diferente.

> [!IMPORTANT]
> Para implementar com sucesso a sua aplicação utilizando o Azure Resource Manager, é importante entender como os recursos são implantados no Azure. No exemplo seguinte, as configurações de nível superior são aplicadas através da utilização **do siteConfig**. É importante definir estas configurações num nível superior, porque transmitem informações ao tempo de funcionamento e ao motor de implantação das Funções. As informações de nível superior são necessárias antes da aplicação dos **controlos de origem/recurso web** da criança. Embora seja possível configurar estas definições no recurso **configuração/appSettings** ao nível da criança, em alguns casos a sua aplicação de função deve ser implementada *antes* **de configurar/appSettings.** Por exemplo, quando está a utilizar funções com [Aplicações Lógicas,](../logic-apps/index.yml)as suas funções são uma dependência de outro recurso.

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
                "value": "~3"
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
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
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
> Este modelo utiliza o valor de definições de aplicações [do Projeto,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) que define o diretório base no qual o motor de implantação de Funções (Kudu) procura código implantável. No nosso repositório, as nossas funções estão numa sub-dobra da pasta **src.** Assim, no exemplo anterior, definimos o valor das definições da aplicação para `src` . Se as suas funções estiverem na raiz do seu repositório, ou se não estiver a implementar a partir do controlo de origem, pode remover o valor das definições desta aplicação.

## <a name="deploy-your-template"></a>Implementar o seu modelo

Pode utilizar qualquer uma das seguintes formas de implantar o seu modelo:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
* [API REST](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botão Implementar no Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` por uma versão [codificada por URL](https://www.bing.com/search?q=url+encode) do caminho bruto do seu ficheiro no `azuredeploy.json` GitHub.

Aqui está um exemplo que usa o markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Aqui está um exemplo que usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implementar com o PowerShell

Os seguintes comandos PowerShell criam um grupo de recursos e implementam um modelo que cria uma aplicação de função com os seus recursos necessários. Para funcionar localmente, tem de ter [a Azure PowerShell](/powershell/azure/install-az-ps) instalada. Corra [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) para se inscrever.

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

Para testar esta implementação, pode utilizar um [modelo como este](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) que cria uma aplicação de função no Windows num plano de Consumo. `<function-app-name>`Substitua-o por um nome único para a sua aplicação de função.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como desenvolver e configurar funções Azure.

* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Como configurar as definições de aplicações de função Azure](functions-how-to-use-azure-function-app-settings.md)
* [Crie a sua primeira função Azure](./functions-get-started.md)

<!-- LINKS -->

[App de função no plano de consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[App de função no plano de serviço de aplicações Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
