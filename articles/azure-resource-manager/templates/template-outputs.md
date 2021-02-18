---
title: Saídas em modelos
description: Descreve como definir valores de saída num modelo de Gestor de Recursos Azure (modelo ARM) e ficheiro Bicep.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 0371a5293b302a2eb0febb010fc16caa8355eb18
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653803"
---
# <a name="outputs-in-arm-templates"></a>Saídas em modelos ARM

Este artigo descreve como definir os valores de saída no seu modelo de Gestor de Recursos Azure (modelo ARM) e ficheiro Bicep. Utiliza-se saídas quando precisa de devolver os valores dos recursos implantados.

O formato de cada valor de saída deve ser resolvido com um dos tipos de [dados.](template-syntax.md#data-types)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Definir valores de saída

O exemplo a seguir mostra como devolver uma propriedade de um recurso implantado.

# <a name="json"></a>[JSON](#tab/json)

Para JSON, adicione a `outputs` secção ao modelo. O valor de saída obtém o nome de domínio totalmente qualificado para um endereço IP público.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Se precisar de fazer uma produção de uma propriedade que tenha um hífen no nome, use suportes em torno do nome em vez de notação de ponto. Por exemplo, use  `['property-name']` em vez de `.property-name` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Para Bicep, use a `output` palavra-chave.

No exemplo seguinte, `publicIP` é o nome simbólico de um endereço IP público implantado no ficheiro Bicep. O valor de saída obtém o nome de domínio totalmente qualificado para o endereço IP público.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Se precisar de fazer uma produção de uma propriedade que tenha um hífen no nome, use suportes em torno do nome em vez de notação de ponto. Por exemplo, use  `['property-name']` em vez de `.property-name` .

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Saída condicional

Pode devolver um valor condicionalmente. Normalmente, usa-se uma saída condicional quando se implanta um recurso [condicionalmente.](conditional-resource-deployment.md) O exemplo a seguir mostra como devolver condicionalmente o ID do recurso para um endereço IP público com base na implementação de um novo:

# <a name="json"></a>[JSON](#tab/json)

Em JSON, adicione o `condition` elemento para definir se a saída é devolvida.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

A saída condicional não está disponível para o Bicep.

No entanto, pode utilizar o `?` operador para devolver um de dois valores dependendo de uma condição.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Para um exemplo simples de saída condicional, consulte [o modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Número dinâmico de saídas

Em alguns cenários, não sabe o número de casos de um valor que precisa de devolver ao criar o modelo. Pode devolver um número variável de valores utilizando a saída iterativa.

# <a name="json"></a>[JSON](#tab/json)

Em JSON, adicione o `copy` elemento para iterar uma saída.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

A produção iterativa não está disponível para o Bicep.

---

Para obter mais informações, consulte [a iteração de saída nos modelos ARM](copy-outputs.md).

## <a name="linked-templates"></a>Modelos ligados

Nos modelos JSON, pode implementar modelos relacionados utilizando [modelos ligados](linked-templates.md). Para recuperar o valor de saída de um modelo ligado, utilize a função [de referência](template-functions-resource.md#reference) no modelo principal. A sintaxe no modelo dos pais é:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

O exemplo a seguir mostra como definir o endereço IP num balanceador de carga recuperando um valor de um modelo ligado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Se o nome da propriedade tiver um hífen, use suportes em torno do nome em vez de notação de ponto.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

Não é possível utilizar a `reference` função na secção de saídas de um [modelo aninhado](linked-templates.md#nested-template). Para devolver os valores de um recurso implantado num modelo aninhado, converta o seu modelo aninhado num modelo ligado.

O [modelo de endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) cria um endereço IP público e produz o ID do recurso. O [modelo do balançador de](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) carga liga-se ao modelo anterior. Utiliza o ID de recurso na saída ao criar o equilibrador de carga.

## <a name="modules"></a>Módulos

Nos ficheiros Bicep, pode implementar modelos relacionados utilizando módulos. Para obter um valor de saída de um módulo, utilize a seguinte sintaxe:

```bicep
<module-name>.outputs.<property-name>
```

O exemplo a seguir mostra como definir o endereço IP num balanceador de carga recuperando um valor de um módulo. O nome do módulo é `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Modelo de exemplo

O modelo a seguir não implementa quaisquer recursos. Mostra algumas formas de devolver saídas de diferentes tipos.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep não suporta loops atualmente.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Obtenha valores de saída

Quando a implementação tiver sucesso, os valores de saída são automaticamente devolvidos nos resultados da implantação.

Para obter valores de saída do histórico de implementação, pode utilizar o script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as propriedades disponíveis para saídas, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
