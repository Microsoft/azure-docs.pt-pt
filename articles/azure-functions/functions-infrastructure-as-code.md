---
title: Implementação de recursos de aplicação de função de função automatizada para O Azure
description: Aprenda a construir um modelo de Gestor de Recursos Azure que implemente a sua aplicação de função.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d98d6fef896f9288be88824a62fa1c8179217f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276897"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implementação de recursos para a sua aplicação de funções em Funções Azure

Pode utilizar um modelo de Gestor de Recursos Azure para implementar uma aplicação de função. Este artigo descreve os recursos e parâmetros necessários para o fazer. Poderá ser necessário implementar recursos adicionais, dependendo dos [gatilhos e encadernações](functions-triggers-bindings.md) na sua aplicação de funções.

Para obter mais informações sobre a criação de modelos, consulte [os modelos de Gestor de Recursos Do Azure.](../azure-resource-manager/templates/template-syntax.md)

Para os modelos de amostra, consulte:
- [App de função no plano de consumo]
- [App de funções no plano de serviço de aplicações do Azure]

## <a name="required-resources"></a>Recursos necessários

Uma implantação de Funções Azure consiste tipicamente nestes recursos:

| Recurso                                                                           | Requisito | Referência de sintaxe e propriedades                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Uma aplicação de função                                                                     | Necessário    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| Uma conta [de armazenamento azure](../storage/index.yml)                                   | Necessário    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| Um componente de Insights de [Aplicação](../azure-monitor/app/app-insights-overview.md) | Opcional    | [Microsoft.Insights/componentes](/azure/templates/microsoft.insights/components)         |   |
| Um [plano de hospedagem](./functions-scale.md)                                             | Opcional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup> Um plano de hospedagem só é necessário quando optar por executar a sua aplicação de funções num [plano Premium](./functions-premium-plan.md) (em pré-visualização) ou num plano de Serviço de [Aplicações](../app-service/overview-hosting-plans.md).

> [!TIP]
> Apesar de não ser necessário, recomenda-se vivamente que configure os Insights de Aplicação para a sua aplicação.

<a name="storage"></a>
### <a name="storage-account"></a>Conta de armazenamento

É necessária uma conta de armazenamento Azure para uma aplicação de função. Precisa de uma conta de propósito geral que suporte bolhas, mesas, filas e ficheiros. Para mais informações, consulte os requisitos da conta de armazenamento da [Azure Functions](storage-considerations.md#storage-account-requirements).

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

Além disso, a propriedade `AzureWebJobsStorage` deve ser especificada como uma configuração de aplicação na configuração do site. Se a aplicação de funções não utilizar os Insights da Aplicação para monitorização, deve também especificar `AzureWebJobsDashboard` como uma definição de aplicação.

O tempo de funcionamento das Funções Azure utiliza a cadeia de ligação `AzureWebJobsStorage` para criar filas internas.  Quando os Insights de Aplicação não estão ativados, o tempo de execução utiliza a cadeia de ligação `AzureWebJobsDashboard` para iniciar sessão no armazenamento da Tabela Azure e alimentar o separador **Monitor** no portal.

Estas propriedades são especificadas na coleção `appSettings` no `siteConfig` objeto:

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

Os Insights de Aplicação são recomendados para monitorizar as suas aplicações de função. O recurso Application Insights é definido com o tipo **Microsoft.Insights/componentes** e o tipo **web:**

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

Além disso, a chave de instrumentação deve ser fornecida na aplicação de funções utilizando a definição de aplicação `APPINSIGHTS_INSTRUMENTATIONKEY`. Esta propriedade é especificada na coleção `appSettings` no objeto `siteConfig`:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plano de hospedagem

A definição do plano de acolhimento varia, podendo ser uma das seguintes:
* [Plano de consumo](#consumption) (padrão)
* [Plano premium](#premium) (em pré-visualização)
* [Plano do Serviço de Aplicações](#app-service-plan)

### <a name="function-app"></a>Function app

O recurso da aplicação de função é definido utilizando um recurso do tipo **Microsoft.Web/sites** e **app de funções**de tipo:

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

Uma aplicação de função deve incluir estas definições de aplicação:

| Nome da definição                 | Descrição                                                                               | Valores de exemplo                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Uma cadeia de ligação a uma conta de armazenamento que o tempo de funcionamento das funções utiliza para a fila interna | Ver [conta de armazenamento](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | A versão do tempo de funcionamento das Funções Azure                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | A pilha de idiomas a ser usada para funções nesta aplicação                                   | `dotnet`, `node`, `java`, `python`ou `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Só é necessário se utilizar a pilha de idiomas `node`, especifica a versão a utilizar              | `10.14.1`                             |

Estas propriedades são especificadas na coleção `appSettings` na propriedade `siteConfig`:

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

## <a name="deploy-on-consumption-plan"></a>Implantação no plano de consumo

O plano de consumo atribui automaticamente a potência do cálculo quando o seu código está em execução, esescala-se conforme necessário para manusear a carga e, em seguida, escala quando o código não está em execução. Não tens de pagar por VMs inativos, e não tens de reservar a capacidade com antecedência. Para saber mais, consulte a escala e o alojamento das [Funções Azure.](functions-scale.md#consumption-plan)

Para obter uma amostra do modelo do Gestor de Recursos Azure, consulte a [App de função no plano de consumo].

### <a name="create-a-consumption-plan"></a>Criar um plano de consumo

Um plano de consumo não precisa de ser definido. Um será automaticamente criado ou selecionado numa base por região quando criar o próprio recurso da aplicação de função.

O plano de Consumo é um tipo especial de recurso "serverfarm". Para windows, pode especificá-lo utilizando o valor `Dynamic` para as propriedades `computeMode` e `sku`:

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

Se definir explicitamente o seu plano de Consumo, terá de definir a propriedade `serverFarmId` na app para que este aponte para a identificação de recursos do plano. Deve certificar-se de que a aplicação de funções também tem uma definição `dependsOn` para o plano.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

#### <a name="windows"></a>Windows

No Windows, um plano de consumo requer duas definições adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Estas propriedades configuram a conta de armazenamento e o caminho do ficheiro onde o código e configuração da aplicação de função são armazenados.

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

No Linux, a aplicação de funções deve ter o seu `kind` definido para `functionapp,linux`, e deve ter a propriedade `reserved` definida para `true`:

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

## <a name="deploy-on-premium-plan"></a>Implementação no plano Premium

O plano Premium oferece a mesma escala que o plano de consumo, mas inclui recursos dedicados e capacidades adicionais. Para saber mais, consulte [o Plano Premium funções do Azure.](./functions-premium-plan.md)

### <a name="create-a-premium-plan"></a>Criar um plano Premium

Um plano Premium é um tipo especial de recurso "serverfarm". Pode especificá-lo utilizando `EP1`, `EP2`ou `EP3` para o valor `Name` propriedade no objeto de [descrição](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)`sku` .

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

### <a name="create-a-function-app"></a>Criar uma aplicação de função

Uma aplicação de função num plano Premium deve ter a `serverFarmId` propriedade definida para o id de recursos do plano criado anteriormente. Além disso, um plano Premium requer duas configurações adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Estas propriedades configuram a conta de armazenamento e o caminho do ficheiro onde o código e configuração da aplicação de função são armazenados.

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

## <a name="deploy-on-app-service-plan"></a>Implementar no plano de serviço de aplicações

No plano do App Service, a sua aplicação de funções funciona em VMs dedicados em SKUs Básicos, Standard e Premium, semelhantes às aplicações web. Para mais detalhes sobre como funciona o plano de serviço de aplicações, consulte os planos do Serviço de [Aplicações Azure em profundidade.](../app-service/overview-hosting-plans.md)

Para obter uma amostra do modelo do Gestor de Recursos Azure, consulte a [App de funções no plano de serviço de aplicações do Azure].

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

Para executar a sua aplicação no Linux, também deve definir o `kind` para `Linux`:

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

Uma aplicação de função num plano de Serviço de Aplicações deve ter a `serverFarmId` propriedade definida para o id de recursos do plano criado anteriormente.

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

As aplicações Linux também devem incluir uma propriedade `linuxFxVersion` sob `siteConfig`. Se estiver apenas a implementar código, o valor para isso é determinado pela sua pilha de tempo de execução desejada:

| Pilha            | Valor de exemplo                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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

Se estiver [a implementar uma imagem de contentor personalizada,](./functions-create-function-linux-custom-image.md)deve especificá-la com `linuxFxVersion` e incluir uma configuração que permita que a sua imagem seja puxada, como na [Aplicação Web para Contentores](/azure/app-service/containers). Além disso, detete `WEBSITES_ENABLE_APP_SERVICE_STORAGE` para `false`, uma vez que o conteúdo da sua aplicação é fornecido no próprio recipiente:

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

## <a name="customizing-a-deployment"></a>Personalização de uma implementação

Uma aplicação de função tem muitos recursos infantis que pode utilizar na sua implementação, incluindo configurações de aplicações e opções de controlo de fontes. Também pode optar por remover o recurso infantil de **controlo de fontes** e utilizar uma opção de [implementação](functions-continuous-deployment.md) diferente.

> [!IMPORTANT]
> Para implementar com sucesso a sua aplicação utilizando o Gestor de Recursos Azure, é importante entender como os recursos são implantados no Azure. No exemplo seguinte, as configurações de alto nível são aplicadas utilizando o **siteConfig**. É importante definir estas configurações a um nível superior, porque transmitem informações para o tempo de funcionamento e o motor de implantação das Funções. São necessárias informações de alto nível antes da aplicação dos **controlos** de fonte/recurso web da criança. Embora seja possível configurar estas definições no recurso **config/appSettings** ao nível da criança, em alguns casos a sua aplicação de função deve ser implementada *antes* da **aplicação de config/appSettings.** Por exemplo, quando está a usar funções com [Aplicações Lógicas,](../logic-apps/index.yml)as suas funções são uma dependência de outro recurso.

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
> Este modelo utiliza o valor de definições da aplicação [project,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) que define o diretório base no qual o motor de implementação de funções (Kudu) procura código implementável. No nosso repositório, as nossas funções estão numa subpasta da pasta **SRC.** Assim, no exemplo anterior, definimos o valor das definições da aplicação para `src`. Se as suas funções estiverem na raiz do seu repositório, ou se não estiver a ser implementada a partir do controlo de origem, pode remover o valor das definições da aplicação.

## <a name="deploy-your-template"></a>Implementar o modelo

Pode utilizar qualquer uma das seguintes formas de implementar o seu modelo:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
* [API REST](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Desdobrar para o botão Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` por uma versão [codificada por URL](https://www.bing.com/search?q=url+encode) do caminho bruto do seu ficheiro `azuredeploy.json` no GitHub.

Aqui está um exemplo que usa o markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Aqui está um exemplo que usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implementar com o PowerShell

Os seguintes comandos PowerShell criam um grupo de recursos e implementam um modelo que cria uma aplicação de função com os recursos necessários. Para funcionar localmente, tem de instalar [o Azure PowerShell.](/powershell/azure/install-az-ps) Corre [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) para assinar.

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

Para testar esta implementação, pode utilizar um [modelo como este](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) que cria uma aplicação de função no Windows num plano de Consumo. Substitua `<function-app-name>` por um nome único para a sua aplicação de funções.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como desenvolver e configurar funções azure.

* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Como configurar as definições da aplicação de função Azure](functions-how-to-use-azure-function-app-settings.md)
* [Crie a sua primeira função Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[App de função no plano de consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[App de funções no plano de serviço de aplicações do Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
