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
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 9fc55e2b3ebb1e932a991e0da2c78a980abbc953
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792502"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implementação de recursos para a sua aplicação de função nas funções do Azure

Pode utilizar um modelo Azure Resource Manager para implementar uma aplicação de funções. Este artigo descreve os recursos necessários e os parâmetros para fazer isso. Poderá ter de implementar recursos adicionais, dependendo do [acionadores e enlaces](functions-triggers-bindings.md) na sua aplicação de função.

Para obter mais informações sobre a criação de modelos, consulte [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para modelos de exemplo, consulte:
- [Aplicação de funções no plano de consumo]
- [Aplicação de funções num plano do serviço de aplicações do Azure]

## <a name="required-resources"></a>Recursos necessários

Uma aplicação de funções requer estes recursos:

* Uma [armazenamento do Azure](../storage/index.yml) conta
* Um plano de alojamento (plano de consumo ou plano de serviço de aplicações)
* Uma aplicação de funções 

Para a sintaxe JSON e propriedades para estes recursos, consulte:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)

### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para uma aplicação de funções. Precisa de uma conta de fins gerais que suporta blobs, tabelas, filas e ficheiros. Para obter mais informações, consulte [requisitos de conta de armazenamento das funções do Azure](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
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
```    

### <a name="hosting-plan"></a>Plano de alojamento

A definição do plano de alojamento varia consoante utilize ou um plano de consumo ou serviço de aplicações. Ver [implementar uma aplicação de funções no plano de consumo](#consumption) e [implementar uma aplicação de funções no plano de serviço de aplicações](#app-service-plan).

### <a name="function-app"></a>Function app

O recurso de aplicação de função é definido usando um recurso do tipo **Microsoft.Web/Site** e o tipo **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Implementar uma aplicação de funções no plano de consumo

Pode executar uma aplicação de funções em dois modos diferentes: o plano de consumo e o plano de serviço de aplicações. O plano de consumo aloca automaticamente a potência de computação quando seu código está em execução, aumenta horizontalmente conforme necessário para processar a carga e, em seguida, aumenta diminui quando o código não está em execução. Então, não precisa de pagar as VMs Inativas, e não tiver a capacidade de reserva com antecedência. Para saber mais sobre os planos de alojamento, veja [planos de consumo de funções do Azure e o serviço de aplicações](functions-scale.md).

Para um modelo do Azure Resource Manager de exemplo, consulte [aplicação de funções no plano de consumo].

### <a name="create-a-consumption-plan"></a>Criar um plano de consumo

Plano de consumo é um tipo especial de recurso de "farm de servidores". Especificou utilizando o `Dynamic` o valor para o `computeMode` e `sku` propriedades:

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

### <a name="create-a-function-app"></a>Criar uma aplicação de função

Além disso, o plano de consumo requer duas definições adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Estas propriedades de configurar o caminho de ficheiro e da conta de armazenamento onde o código de aplicação de função e a configuração são armazenadas.

```json
{
    "apiVersion": "2015-08-01",
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
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
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
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Implementar uma aplicação de funções no plano de serviço de aplicações

No plano do serviço de aplicações, a aplicação function app é executada em VMs dedicadas no básico, Standard e Premium SKUs, semelhante aos aplicativos web. Para obter detalhes sobre como funciona o plano do serviço de aplicações, consulte a [descrição geral aprofundada dos planos do App Service do Azure](../app-service/overview-hosting-plans.md). 

Para um modelo do Azure Resource Manager de exemplo, consulte [aplicação de funções num plano do serviço de aplicações do Azure].

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

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

### <a name="create-a-function-app"></a>Criar uma aplicação de função 

Depois de selecionar uma opção de dimensionamento, crie uma aplicação de funções. A aplicação é o contentor que retém todas as suas funções.

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
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
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
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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
