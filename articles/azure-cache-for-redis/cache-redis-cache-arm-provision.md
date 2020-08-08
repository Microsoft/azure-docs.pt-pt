---
title: Implementar cache Azure para Redis com gestor de recursos Azure
description: Aprenda a usar um modelo de Gestor de Recursos Azure para implementar uma Cache Azure para recurso Redis. Os modelos são fornecidos para cenários comuns.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 2d00a6b7753a61bb2527a56231b2fe054736f1b0
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008581"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Crie uma cache Azure para Redis usando um modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Neste tópico, você aprende a criar um modelo de Gestor de Recursos Azure que implementa uma Cache Azure para Redis. A cache pode ser usada com uma conta de armazenamento existente para manter os dados de diagnóstico. Também aprende a definir quais os recursos que são implantados e como definir parâmetros especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Atualmente, as definições de diagnóstico são partilhadas para todos os caches da mesma região para uma subscrição. A atualização de uma cache na região afeta todos os outros caches da região.

Para obter mais informações sobre a criação de modelos, consulte [os modelos do Gestor de Recursos Azure.](../azure-resource-manager/templates/template-syntax.md) Para saber mais sobre a sintaxe JSON e propriedades para tipos de recursos de cache, consulte os [tipos de recursos Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

Para o modelo completo, consulte [Azure Cache para o modelo Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Os modelos de Gestor de Recursos para o novo [nível Premium](cache-overview.md#service-tiers) estão disponíveis. 
> 
> * [Criar uma Cache Premium Azure para Redis com agrupamento](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Criar Cache Premium Azure para Redis com persistência de dados](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Criar Cache Premium Redis implantado numa Rede Virtual](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Para verificar os modelos mais recentes, consulte [os Modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/) e procure `Azure Cache for Redis` .
> 
> 

## <a name="what-you-will-deploy"></a>O que vai implementar
Neste modelo, irá implementar uma Cache Azure para Redis que utiliza uma conta de armazenamento existente para dados de diagnóstico.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção chamada Parâmetros que contém todos os valores dos parâmetros.
Deverá definir um parâmetro para esses valores que variam com base no projeto ou no ambiente que está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocação
A localização da Cache Azure para Redis. Para um melhor desempenho, utilize o mesmo local que a aplicação a ser usada com a cache.

```json
  "redisCacheLocation": {
    "type": "string"
  }
```

### <a name="existingdiagnosticsstorageaccountname"></a>DiagnósticosStorageScon
O nome da conta de armazenamento existente para usar para diagnósticos. 

```json
  "existingDiagnosticsStorageAccountName": {
    "type": "string"
  }
```

### <a name="enablenonsslport"></a>enableNonSslPort
Um valor booleano que indica se permite o acesso através de portas não SSL.

```json
  "enableNonSslPort": {
    "type": "bool"
  }
```

### <a name="diagnosticsstatus"></a>diagnósticosStatus
Um valor que indica se os diagnósticos estão ativados. Use ON ou OFF.

```json
  "diagnosticsStatus": {
    "type": "string",
    "defaultValue": "ON",
    "allowedValues": [
          "ON",
          "OFF"
      ]
  }
```

## <a name="resources-to-deploy"></a>Recursos a implementar
### <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
Cria a Cache Azure para Redis.

```json
  {
    "apiVersion": "2015-08-01",
    "name": "[parameters('redisCacheName')]",
    "type": "Microsoft.Cache/Redis",
    "location": "[parameters('redisCacheLocation')]",
    "properties": {
      "enableNonSslPort": "[parameters('enableNonSslPort')]",
      "sku": {
        "capacity": "[parameters('redisCacheCapacity')]",
        "family": "[parameters('redisCacheFamily')]",
        "name": "[parameters('redisCacheSKU')]"
      }
    },
    "resources": [
      {
        "apiVersion": "2017-05-01-preview",
        "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
        "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
        "location": "[parameters('redisCacheLocation')]",
        "dependsOn": [
          "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
        ],
        "properties": {
          "status": "[parameters('diagnosticsStatus')]",
          "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
        }
      }
    ]
  }
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
