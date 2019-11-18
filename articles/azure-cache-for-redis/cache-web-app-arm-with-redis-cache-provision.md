---
title: Provisionar aplicativo Web com o cache do Azure para Redis
description: Use Azure Resource Manager modelo para implantar o aplicativo Web com o cache do Azure para Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 8e15d51062993bc6e9913d49d3fe67c1a8b9cd03
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122629"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Criar um aplicativo Web mais o cache do Azure para Redis usando um modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Neste tópico, você aprenderá a criar um modelo de Azure Resource Manager que implanta um aplicativo Web do Azure com o cache do Azure para Redis. Você aprenderá como definir quais recursos são implantados e como definir parâmetros que são especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Para obter mais informações sobre como criar modelos, consulte Criando [modelos de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Para saber mais sobre a sintaxe JSON e propriedades para tipos de recursos de cache, consulte [tipos de recurso Microsoft. cache](/azure/templates/microsoft.cache/allversions).

Para obter o modelo completo, consulte o [aplicativo Web com o cache do Azure para o modelo Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>O que você vai implantar
Neste modelo, você implantará:

* Aplicação Web do Azure
* Cache do Azure para Redis

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parâmetros a serem especificados
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Variáveis para nomes
Este modelo usa variáveis para construir nomes para os recursos. Ele usa a função [uniquestring](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) para construir um valor com base na ID do grupo de recursos.

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Recursos a implementar
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
Cria o cache do Azure para Redis que é usado com o aplicativo Web. O nome do cache é especificado na variável **CacheName** .

O modelo cria o cache no mesmo local que o grupo de recursos.

    {
      "name": "[variables('cacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [ ],
      "tags": {
        "displayName": "cache"
      },
      "properties": {
        "sku": {
          "name": "[parameters('cacheSKUName')]",
          "family": "[parameters('cacheSKUFamily')]",
          "capacity": "[parameters('cacheSKUCapacity')]"
        }
      }
    }


### <a name="web-app"></a>Aplicação Web
Cria o aplicativo Web com o nome especificado na variável **WebSiteName** .

Observe que o aplicativo Web está configurado com propriedades de configuração de aplicativo que o habilitam a trabalhar com o cache do Azure para Redis. Essas configurações de aplicativo são criadas dinamicamente com base nos valores fornecidos durante a implantação.

    {
      "apiVersion": "2015-08-01",
      "name": "[variables('webSiteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
        "displayName": "Website"
      },
      "properties": {
        "name": "[variables('webSiteName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "appsettings",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
            "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
          ],
          "properties": {
            "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI do Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
