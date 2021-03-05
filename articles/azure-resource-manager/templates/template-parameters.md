---
title: Parâmetros em modelos
description: Descreve como definir parâmetros num modelo de Gestor de Recursos Azure (modelo ARM) e ficheiro Bicep.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: a8608e4733960d7f5ba7e5f548c47f16a2b244bb
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123399"
---
# <a name="parameters-in-arm-templates"></a>Parâmetros em modelos ARM

Este artigo descreve como definir e usar parâmetros no seu modelo de Gestor de Recursos Azure (modelo ARM) e ficheiro Bicep. Ao fornecer diferentes valores para parâmetros, pode reutilizar um modelo para diferentes ambientes.

O Gestor de Recursos resolve os valores dos parâmetros antes de iniciar as operações de implantação. Onde quer que o parâmetro seja utilizado no modelo, o Gestor de Recursos substitui-o pelo valor resolvido.

Cada parâmetro deve ser definido para um dos tipos de [dados](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>Declaração mínima

No mínimo, cada parâmetro precisa de um nome e tipo. Em Bicep, um parâmetro não pode ter o mesmo nome que uma variável, recurso, saída ou outro parâmetro no mesmo âmbito.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>Parâmetros seguros

Pode marcar os parâmetros de cordas ou objetos como seguros. O valor de um parâmetro seguro não é guardado para o histórico de implantação e não está registado.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>Valores permitidos

Pode definir valores permitidos para um parâmetro. Fornece os valores permitidos numa matriz. A implementação falha durante a validação se um valor for passado para o parâmetro que não é um dos valores permitidos.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>Valor predefinido

Pode especificar um valor predefinido para um parâmetro. O valor predefinido é utilizado quando um valor não é fornecido durante a implementação.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

Para especificar um valor predefinido juntamente com outras propriedades para o parâmetro, utilize a seguinte sintaxe.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

Pode utilizar expressões com o valor predefinido. Não é possível utilizar a função [de referência](template-functions-resource.md#reference) ou qualquer das funções da [lista](template-functions-resource.md#list) na secção de parâmetros. Estas funções obtêm o estado de funcionamento de um recurso, e não podem ser executadas antes da implementação quando os parâmetros são resolvidos.

Expressões não são permitidas com outras propriedades de parâmetros.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Pode utilizar outro valor de parâmetro para construir um valor padrão. O modelo a seguir constrói um nome de plano de anfitrião a partir do nome do site.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>Restrições de comprimento

Pode especificar comprimentos mínimos e máximos para parâmetros de corda e matriz. Pode definir um ou ambos os constrangimentos. Para as cordas, o comprimento indica o número de caracteres. Para as matrizes, o comprimento indica o número de itens na matriz.

O exemplo a seguir declara dois parâmetros. Um parâmetro é para um nome de conta de armazenamento que deve ter 3-24 caracteres. O outro parâmetro é uma matriz que deve ter de 1-5 itens.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>Constrangimentos inteiros

Pode definir valores mínimos e máximos para os parâmetros inteiros. Pode definir um ou ambos os constrangimentos.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>Descrição

Pode adicionar uma descrição a um parâmetro para ajudar os utilizadores do seu modelo a compreender o valor a fornecer. Ao implementar o modelo através do portal, o texto que fornece na descrição é automaticamente utilizado como uma dica para esse parâmetro. Adicione apenas uma descrição quando o texto fornecer mais informações do que pode ser deduzido a partir do nome do parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>Use o parâmetro

Num modelo JSON, refere-se o valor do parâmetro utilizando a função [de parâmetros.](template-functions-deployment.md#parameters) Em Bicep, usa-se o nome do parâmetro. O exemplo a seguir utiliza um valor de parâmetro para um nome de Cofre de Chaves.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados passando-os como um objeto. Esta abordagem também reduz o número de parâmetros no modelo.

O exemplo a seguir mostra um parâmetro que é um objeto. O valor predefinido mostra as propriedades esperadas para o objeto. Estas propriedades são utilizadas na definição do recurso a utilizar.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir demonstram cenários para a utilização de parâmetros.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções para valores predefinidos](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como utilizar as funções do modelo ao definir valores predefinidos para parâmetros. O modelo não implementa recursos. Constrói valores de parâmetros e devolve esses valores. |
|[objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra usar um objeto para um parâmetro. O modelo não implementa recursos. Constrói valores de parâmetros e devolve esses valores. |

## <a name="next-steps"></a>Passos seguintes

* Para conhecer as propriedades disponíveis para parâmetros, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
* Para saber como passar os valores dos parâmetros como ficheiro, consulte [o ficheiro de parâmetros Do Gestor de Recursos](parameter-files.md).
* Para recomendações sobre a criação de parâmetros, consulte [as melhores práticas - parâmetros.](template-best-practices.md#parameters)
