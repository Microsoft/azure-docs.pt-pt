---
title: Funções de modelo de Azure Resource Manager – implantação | Microsoft Docs
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager para recuperar informações de implantação.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: tomfitz
ms.openlocfilehash: 236fbb9e4ed3283ecf9147e6eb5033fb906a127b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194345"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funções de implantação para modelos de Azure Resource Manager 

O Gerenciador de recursos fornece as seguintes funções para obter valores de seções do modelo e valores relacionados à implantação:

* [deployment](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Para obter valores de recursos, grupos de recursos ou assinaturas, consulte funções de [recurso](resource-group-template-functions-resource.md).

<a id="deployment" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment"></a>deployment
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

Quando você [implanta em uma assinatura do Azure](deploy-to-subscription.md), em vez de um grupo de recursos, o objeto `location` de retorno inclui uma propriedade. A propriedade Location é incluída durante a implantação de um modelo local ou de um modelo externo.

### <a name="remarks"></a>Observações

Você pode usar a implantação () para vincular a outro modelo com base no URI do modelo pai.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Se você reimplantar um modelo do histórico de implantação no portal, o modelo será implantado como um arquivo local. A `templateLink` propriedade não é retornada na função de implantação. Se o modelo se basear `templateLink` para construir um link para outro modelo, não use o portal para reimplantar. Em vez disso, use os comandos usados para implantar o modelo originalmente.

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

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Para um modelo de nível de assinatura que usa a função de implantação, consulte [função de implantação de assinatura](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Ele é implantado `az deployment create` com `New-AzDeployment` um ou mais comandos.

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Retorna um valor de parâmetro. O nome do parâmetro especificado deve ser definido na seção de parâmetros do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |Cadeia de caracteres |O nome do parâmetro a ser retornado. |

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

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| stringOutput | Cadeia | opção 1 |
| intOutput | Int | 1 |
| objectOutput | Objeto | {"One": "a", "Two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | Cadeia | opção 1 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>as
`variables(variableName)`

Retorna o valor da variável. O nome de variável especificado deve ser definido na seção de variáveis do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| variableName |Sim |Cadeia |O nome da variável a ser retornada. |

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

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| exampleOutput1 | Cadeia | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | Cadeia | myVariable |
| exampleOutput4 |  Objeto | {"Property1": "value1", "Property2": "value2"} |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, consulte [usando modelos vinculados com Azure Resource Manager](resource-group-linked-templates.md).
* Para fazer a iteração de um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver como implementar o modelo que criou, veja [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).

