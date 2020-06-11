---
title: Funções do modelo - implantação
description: Descreve as funções a utilizar num modelo do Gestor de Recursos Azure para recuperar informações de implantação.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e8240c05cba82d5563c4b327ecbc65a9c358720f
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677819"
---
# <a name="deployment-functions-for-arm-templates"></a>Funções de implantação para modelos ARM

O Gestor de Recursos fornece as seguintes funções para obter valores relacionados com a implementação atual do seu modelo Azure Resource Manager (ARM):

* [implantação](#deployment)
* [ambiente](#environment)
* [parâmetros](#parameters)
* [variáveis](#variables)

Para obter valores de recursos, grupos de recursos ou subscrições, consulte [as funções de Recursos](template-functions-resource.md).

## <a name="deployment"></a>implementação

`deployment()`

Devolve informações sobre a operação de implantação atual.

### <a name="return-value"></a>Valor devolvido

Esta função devolve o objeto que é passado durante a implantação. As propriedades no objeto devolvido diferem com base no facto de ser:

* implantando um modelo que é um arquivo local ou implantando um modelo que é um ficheiro remoto acedido através de um URI.
* implantação num grupo de recursos ou implantação para um dos outros âmbitos[(assinatura Azure,](deploy-to-subscription.md) [grupo de gestão,](deploy-to-management-group.md)ou [inquilino).](deploy-to-tenant.md)

Ao implementar um modelo local para um grupo de recursos: a função devolve o seguinte formato:

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
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Ao implementar um modelo remoto para um grupo de recursos: a função devolve o seguinte formato:

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
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando você implementa para uma subscrição Azure, grupo de gestão ou inquilino, o objeto de retorno inclui um `location` imóvel. A propriedade de localização está incluída ao implementar um modelo local ou um modelo externo. O formato é:

```json
{
    "name": "",
    "location": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Observações

Pode utilizar a implementação para ligar a outro modelo baseado no URI do modelo principal.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Se recolocar um modelo a partir do histórico de implementação no portal, o modelo é implementado como um ficheiro local. A `templateLink` propriedade não é devolvida na função de implantação. Se o seu modelo depender `templateLink` de construir uma ligação a outro modelo, não use o portal para recolocar. Em vez disso, utilize os comandos utilizados para implantar originalmente o modelo.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a seguir devolve o objeto de implantação:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "deploymentOutput": {
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
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>environment

`environment()`

Devolve informações sobre o ambiente Azure utilizado para implantação.

### <a name="return-value"></a>Valor devolvido

Esta função devolve propriedades para o ambiente Azure atual. O exemplo a seguir mostra as propriedades do Azure global. Nuvens soberanas podem devolver propriedades ligeiramente diferentes.

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

O modelo de exemplo a seguir devolve o objeto ambiente.

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

O exemplo anterior devolve o seguinte objeto quando implantado no Azure global:

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
| nome de parâmetroName |Sim |string |O nome do parâmetro para voltar. |

### <a name="return-value"></a>Valor devolvido

O valor do parâmetro especificado.

### <a name="remarks"></a>Observações

Normalmente, utiliza-se parâmetros para definir valores de recursos. O exemplo a seguir define o nome do web site para o valor do parâmetro transmitido durante a implantação.

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

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) a seguir mostra uma utilização simplificada da função dos parâmetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores predefinidos é:

| Name | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | opção 1 |
| intOutput | int | 1 |
| objetoOutput | Objeto | {"one": "a", "two": "b"} |
| intervalo de matriz | Matriz | [1, 2, 3] |
| crossOutput | String | opção 1 |

Para obter mais informações sobre a utilização de parâmetros, consulte [parâmetros no modelo do Gestor de Recursos Azure](template-parameters.md).

## <a name="variables"></a>variáveis

`variables(variableName)`

Devolve o valor da variável. O nome variável especificado deve ser definido na secção de variáveis do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| nome variável |Sim |String |O nome da variável para devolver. |

### <a name="return-value"></a>Valor devolvido

O valor da variável especificada.

### <a name="remarks"></a>Observações

Normalmente, você usa variáveis para simplificar o seu modelo construindo valores complexos apenas uma vez. O exemplo a seguir constrói um nome único para uma conta de armazenamento.

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

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) a seguir devolve diferentes valores variáveis.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores predefinidos é:

| Name | Tipo | Valor |
| ---- | ---- | ----- |
| exemploOutput1 | String | myVariable |
| exemploOutput2 | Matriz | [1, 2, 3, 4] |
| exemploOutput3 | String | myVariable |
| exemploOutput4 |  Objeto | {"property1": "value1", "property2": "value2"} |

Para obter mais informações sobre a utilização de variáveis, consulte [Variáveis no modelo do Gestor de Recursos Azure](template-variables.md).

## <a name="next-steps"></a>Próximos passos

* Para obter uma descrição das secções num modelo de Gestor de Recursos Azure, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
