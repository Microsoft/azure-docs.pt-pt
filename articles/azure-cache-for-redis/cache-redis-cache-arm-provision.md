---
title: Implantar o cache do Azure para Redis com o Azure Resource Manager
description: Saiba como usar um modelo de Azure Resource Manager para implantar um cache do Azure para o recurso Redis. Os modelos são fornecidos para cenários comuns.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412413"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Criar um cache do Azure para Redis usando um modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Neste tópico, você aprenderá a criar um modelo de Azure Resource Manager que implanta um cache do Azure para Redis. O cache pode ser usado com uma conta de armazenamento existente para manter os dados de diagnóstico. Você também aprende como definir quais recursos são implantados e como definir parâmetros que são especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Atualmente, as configurações de diagnóstico são compartilhadas para todos os caches na mesma região para uma assinatura. A atualização de um cache na região afeta todos os outros caches na região.

Para obter mais informações sobre como criar modelos, consulte Criando [modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Para saber mais sobre a sintaxe JSON e propriedades para tipos de recursos de cache, consulte [tipos de recurso Microsoft. cache](/azure/templates/microsoft.cache/allversions).

Para obter o modelo completo, consulte o [cache do Azure para o modelo Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Os modelos do Resource Manager para a nova [camada Premium](cache-premium-tier-intro.md) estão disponíveis. 
> 
> * [Criar um cache do Azure Premium para Redis com clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Criar um cache do Azure Premium para Redis com persistência de dados](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Criar um cache Redis Premium implantado em uma rede virtual](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Para verificar os modelos mais recentes, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) e pesquise por `Azure Cache for Redis`.
> 
> 

## <a name="what-you-will-deploy"></a>O que você vai implantar
Neste modelo, você implantará um cache do Azure para Redis que usa uma conta de armazenamento existente para dados de diagnóstico.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma seção chamada parâmetros que contém todos os valores de parâmetro.
Deverá definir um parâmetro para esses valores que variam com base no projeto ou no ambiente que está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
O local do cache do Azure para Redis. Para obter o melhor desempenho, use o mesmo local que o aplicativo a ser usado com o cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
O nome da conta de armazenamento existente a ser usada para diagnóstico. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Um valor booliano que indica se o acesso deve ser permitido via portas não SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Um valor que indica se o diagnóstico está habilitado. Use ativar ou desativar.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Recursos a implementar
### <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
Cria o cache do Azure para Redis.

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

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>CLI do Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
