---
title: Compare a sintaxe para modelos de Gestor de Recursos Azure em JSON e Bicep
description: Compara os modelos do Azure Resource Manager desenvolvidos com JSON e Bicep, e mostra como se converter entre os idiomas.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461799"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparando JSON e Bicep para modelos

Este artigo compara a sintaxe Bicep com a sintaxe JSON para os modelos Azure Resource Manager (modelos ARM). Na maioria dos casos, bicep fornece sintaxe que é menos verbose do que o equivalente em JSON.

Se estiver familiarizado com o uso de JSON para desenvolver modelos ARM, use os seguintes exemplos para aprender sobre a sintaxe equivalente para Bicep.

## <a name="expressions"></a>Expressions (Expressões)

Para autor de uma expressão:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parâmetros

Para declarar um parâmetro com um valor padrão:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Para obter um valor de parâmetro:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Variáveis

Para declarar uma variável:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Para obter um valor variável:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Cadeias

Para concatenar cordas:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Operadores lógicos

Para devolver o **lógico E:**

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Para definir condicionalmente um valor:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Âmbito de implantação

Para definir o âmbito-alvo da implantação:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Recursos

Para declarar um recurso:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Para implementar condicionalmente um recurso:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Para definir a propriedade de recursos:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Para obter iD de recurso de recurso no modelo:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Ciclos

Para iterar sobre itens numa matriz ou contagem:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Dependências de recursos

Para definir a dependência entre os recursos:

Para o Bicep, ou depende da deteção automática de dependências ou da dependência manual.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Recursos de referência

Para obter uma propriedade a partir de um recurso no modelo:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Para obter uma propriedade de um recurso existente que não é implantado no modelo:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Saídas

Para des output uma propriedade a partir de um recurso no modelo:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Reutilização de código

Para separar uma solução em vários ficheiros:

* Para Bicep, utilize [módulos.](bicep-tutorial-add-modules.md)
* Para JSON, utilize [modelos ligados](linked-templates.md).

## <a name="recommendations"></a>Recomendações

* Quando possível, evite utilizar as funções [de referência](template-functions-resource.md#reference) e [recursosId](template-functions-resource.md#resourceid) no seu ficheiro Bicep. Quando referenciar um recurso na mesma implantação bicep, utilize o identificador de recursos. Por exemplo, se implementou um recurso no seu ficheiro Bicep `stg` como identificador de recursos, use sintaxe como `stg.id` ou para obter `stg.properties.primaryEndpoints.blob` valores de propriedade. Ao utilizar o identificador de recursos, cria-se uma dependência implícita entre recursos. Não precisa definir explicitamente a dependência com a propriedade dependOn.
* Se o recurso não for implantado no ficheiro Bicep, ainda pode obter uma referência simbólica ao recurso utilizando a palavra-chave **existente.**
* Utilize invólucros consistentes para identificadores. Se não tem certeza de que tipo de invólucro usar, experimente o invólucro de camelo. Por exemplo, `param myCamelCasedParameter string`.
* Adicione uma descrição a um parâmetro apenas quando a descrição fornecer informações essenciais aos utilizadores. Pode utilizar `//` comentários para obter algumas informações.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Bicep, consulte o [tutorial de Bicep.](./bicep-tutorial-create-first-bicep.md)
* Para aprender sobre a conversão de modelos entre as línguas, consulte [os modelos DE CONVERSÃO ARM entre JSON e Bicep](bicep-decompile.md).
