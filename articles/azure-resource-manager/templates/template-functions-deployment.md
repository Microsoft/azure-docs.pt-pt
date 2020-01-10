---
title: Funções de modelo – implantação
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager para recuperar informações de implantação.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f2939efc9481255233fbdda29f4fa30adcd24b7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484041"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funções de implantação para modelos de Azure Resource Manager 

O Gerenciador de recursos fornece as seguintes funções para obter valores relacionados à implantação atual:

* [deployment](#deployment)
* [ambiente](#environment)
* [parameters](#parameters)
* [variables](#variables)

Para obter valores de recursos, grupos de recursos ou assinaturas, consulte funções de [recurso](template-functions-resource.md).

## <a name="deployment"></a>implementação

`deployment()`

Retorna informações sobre a operação de implantação atual.

### <a name="return-value"></a>Valor de retorno

Essa função retorna o objeto que é passado durante a implantação. As propriedades no objeto retornado diferem com base em se o objeto de implantação é passado como um link ou como um objeto embutido. Quando o objeto de implantação é passado em linha, como ao usar o parâmetro **-TemplateFile** em Azure PowerShell para apontar para um arquivo local, o objeto retornado tem o seguinte formato:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando o objeto é passado como um link, como ao usar o parâmetro **-TemplateUri** para apontar para um objeto remoto, o objeto é retornado no seguinte formato: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando você [implanta em uma assinatura do Azure](deploy-to-subscription.md), em vez de um grupo de recursos, o objeto de retorno inclui uma propriedade `location`. A propriedade Location é incluída durante a implantação de um modelo local ou de um modelo externo.

### <a name="remarks"></a>Observações

Você pode usar a implantação () para vincular a outro modelo com base no URI do modelo pai.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Se você reimplantar um modelo do histórico de implantação no portal, o modelo será implantado como um arquivo local. A propriedade `templateLink` não é retornada na função de implantação. Se o modelo depender de `templateLink` para construir um link para outro modelo, não use o portal para reimplantar. Em vez disso, use os comandos usados para implantar o modelo originalmente.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a seguir retorna o objeto de implantação:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

O exemplo anterior retorna o seguinte objeto:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Para um modelo de nível de assinatura que usa a função de implantação, consulte [função de implantação de assinatura](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Ele é implantado com comandos `az deployment create` ou `New-AzDeployment`.

## <a name="environment"></a>environment

`environment()`

Retorna informações sobre o ambiente do Azure usado para implantação.

### <a name="return-value"></a>Valor de retorno

Essa função retorna propriedades para o ambiente atual do Azure. O exemplo a seguir mostra as propriedades do Azure global. Nuvens soberanas podem retornar Propriedades ligeiramente diferentes.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Exemplo

O modelo de exemplo a seguir retorna o objeto de ambiente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

O exemplo anterior retorna o seguinte objeto quando implantado no Azure global:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parâmetros

`parameters(parameterName)`

Retorna um valor de parâmetro. O nome do parâmetro especificado deve ser definido na seção de parâmetros do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro a ser retornado. |

### <a name="return-value"></a>Valor de retorno

O valor do parâmetro especificado.

### <a name="remarks"></a>Observações

Normalmente, você usa parâmetros para definir valores de recurso. O exemplo a seguir define o nome do site para o valor do parâmetro passado durante a implantação.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) a seguir mostra um uso simplificado da função Parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | opção 1 |
| intOutput | Int | 1 |
| objectOutput | Objeto | {"One": "a", "Two": "b"} |
| arrayOutput | Matriz | [1, 2, 3] |
| crossOutput | String | opção 1 |

Para obter mais informações sobre como usar parâmetros, consulte [parâmetros no modelo Azure Resource Manager](template-parameters.md).

## <a name="variables"></a>as

`variables(variableName)`

Retorna o valor da variável. O nome de variável especificado deve ser definido na seção de variáveis do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| variableName |Sim |String |O nome da variável a ser retornada. |

### <a name="return-value"></a>Valor de retorno

O valor da variável especificada.

### <a name="remarks"></a>Observações

Normalmente, você usa variáveis para simplificar o modelo construindo valores complexos apenas uma vez. O exemplo a seguir constrói um nome exclusivo para uma conta de armazenamento.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) a seguir retorna valores de variáveis diferentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Matriz | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Objeto | {"Property1": "value1", "Property2": "value2"} |

Para obter mais informações sobre como usar variáveis, consulte [variáveis no modelo Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Passos seguintes
* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](template-syntax.md).
* Para mesclar vários modelos, consulte [usando modelos vinculados com Azure Resource Manager](linked-templates.md).
* Para fazer a iteração de um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](create-multiple-instances.md).
* Para ver como implementar o modelo que criou, veja [implementar uma aplicação com o modelo Azure Resource Manager](deploy-powershell.md).

