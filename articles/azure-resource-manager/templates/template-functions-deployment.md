---
title: Funções do modelo - implantação
description: Descreve as funções a utilizar num modelo do Gestor de Recursos Azure para recuperar informações de implementação.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: b241aaf43ee3204c9960d0099ce3c61d4c1a80ee
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274271"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funções de implantação para modelos de Gestor de Recursos Azure 

O Gestor de Recursos fornece as seguintes funções para obter valores relacionados com a implementação atual:

* [implantação](#deployment)
* [ambiente](#environment)
* [parameters](#parameters)
* [variáveis](#variables)

Para obter valores de recursos, grupos de recursos ou subscrições, consulte funções de [Recursos.](template-functions-resource.md)

## <a name="deployment"></a>implementação

`deployment()`

Devolve informações sobre a operação de implantação atual.

### <a name="return-value"></a>Valor de retorno

Esta função devolve o objeto que é passado durante a implantação. As propriedades do objeto devolvido diferem com base no facto de o objeto de implantação ser passado como um link ou como um objeto em linha. Quando o objeto de implantação é passado em linha, como quando se utiliza o parâmetro **-TemplateFile** em Azure PowerShell para apontar para um ficheiro local, o objeto devolvido tem o seguinte formato:

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

Quando o objeto é passado como um link, como quando se utiliza o parâmetro **-TemplateUri** para apontar para um objeto remoto, o objeto é devolvido no seguinte formato: 

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

Quando [se implementa para uma subscrição Azure](deploy-to-subscription.md), em vez de um grupo de recursos, o objeto de retorno inclui uma propriedade `location`. A propriedade de localização está incluída ao implementar um modelo local ou um modelo externo.

### <a name="remarks"></a>Observações

Pode utilizar a implementação() para ligar a outro modelo com base no URI do modelo de progenitor.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Se reimplantar um modelo do histórico de implementação no portal, o modelo é implantado como um ficheiro local. A propriedade `templateLink` não é devolvida na função de implantação. Se o seu modelo depender de `templateLink` para construir uma ligação a outro modelo, não utilize o portal para reimplantar. Em vez disso, use os comandos que usou para implantar originalmente o modelo.

### <a name="example"></a>Exemplo

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) de exemplo devolve o objeto de implantação:

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

O exemplo anterior devolve o seguinte objeto:

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

Para um modelo de nível de subscrição que utiliza a função de implementação, consulte a função de implementação de [subscrição](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Está implantado com comandos `az deployment create` ou `New-AzDeployment`.

## <a name="environment"></a>environment

`environment()`

Devolve informações sobre o ambiente Azure utilizado para a implantação.

### <a name="return-value"></a>Valor de retorno

Esta função devolve propriedades para o ambiente atual do Azure. O exemplo que se segue mostra as propriedades para o Azure global. Nuvens soberanas podem devolver propriedades ligeiramente diferentes.

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

O modelo de exemplo seguinte devolve o objeto ambiental.

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

O exemplo anterior devolve o seguinte objeto quando implantado no Global Azure:

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

Devolve um valor de parâmetro. O nome do parâmetro especificado deve ser definido na secção de parâmetros do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro para voltar. |

### <a name="return-value"></a>Valor de retorno

O valor do parâmetro especificado.

### <a name="remarks"></a>Observações

Normalmente, utiliza-se parâmetros para definir valores de recursos. O exemplo seguinte define o nome do web site para o valor do parâmetro passado durante a implementação.

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

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) de exemplo seguinte mostra uma utilização simplificada da função de parâmetros.

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
| objectOutput | Object | {"um": "a", "dois": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | opção 1 |

Para obter mais informações sobre a utilização de parâmetros, consulte [parâmetros no modelo do Gestor](template-parameters.md)de Recursos Azure .

## <a name="variables"></a>variáveis

`variables(variableName)`

Devolve o valor da variável. O nome variável especificado deve ser definido na secção variável do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| variableName |Sim |String |O nome da variável para voltar. |

### <a name="return-value"></a>Valor de retorno

O valor da variável especificada.

### <a name="remarks"></a>Observações

Normalmente, você usa variáveis para simplificar o seu modelo construindo valores complexos apenas uma vez. O exemplo seguinte constrói um nome único para uma conta de armazenamento.

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

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) de exemplo seguinte devolve diferentes valores variáveis.

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
| exampleOutput1 | String | myVariável |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariável |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Para obter mais informações sobre a utilização de variáveis, consulte [variáveis no modelo de Gestor](template-variables.md)de Recursos Azure .

## <a name="next-steps"></a>Passos seguintes
* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte os modelos de [Gestor de Recursos Azure da Autoria](template-syntax.md).
* Para fundir vários modelos, consulte [Utilizar modelos ligados com](linked-templates.md)o Gestor de Recursos Azure .
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo de Gestor](deploy-powershell.md)de Recursos Azure .

