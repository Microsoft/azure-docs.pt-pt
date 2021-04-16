---
title: Implementar múltiplas instâncias de recursos
description: Utilize a operação de cópia e as matrizes num modelo de Gestor de Recursos Azure (modelo ARM) para implementar o tipo de recurso muitas vezes.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479972"
---
# <a name="resource-iteration-in-arm-templates"></a>Iteração de recursos em modelos ARM

Este artigo mostra-lhe como criar mais de um exemplo de um recurso no seu modelo de Gestor de Recursos Azure (modelo ARM). Ao adicionar o ciclo de cópia à secção de recursos do seu modelo, pode definir dinamicamente o número de recursos a utilizar. Evite também ter de repetir a sintaxe do modelo.

Também pode utilizar o ciclo de cópias com [propriedades,](copy-properties.md) [variáveis](copy-variables.md)e [saídas.](copy-outputs.md)

Se precisar de especificar se um recurso é implantado, consulte o [elemento da condição](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Adicione o `copy` elemento à secção de recursos do seu modelo para implementar várias instâncias do recurso. O `copy` elemento tem o seguinte formato geral:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A `name` propriedade é qualquer valor que identifique o loop. A `count` propriedade especifica o número de iterações que deseja para o tipo de recurso.

Utilize as `mode` propriedades e propriedades para `batchSize` especificar se os recursos são implantados em paralelo ou em sequência. Estas propriedades são descritas em [Série ou Paralelo.](#serial-or-parallel)

# <a name="bicep"></a>[Bicep](#tab/bicep)

Os loops podem ser utilizados declarando múltiplos recursos através de:

- Iterando sobre uma matriz:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Iterando sobre os elementos de uma matriz

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Usando índice de loop

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode ultrapassar os 800.

A contagem não pode ser um número negativo. Pode ser zero se implementar o modelo com uma versão recente de Azure CLI, PowerShell ou REST API. Especificamente, deve usar:

- Azure PowerShell **2.6** ou mais tarde
- Azure CLI **2.0.74** ou mais tarde
- REST Versão API **2019-05-10** ou posterior
- [As implementações ligadas](linked-templates.md) devem utilizar a versão API **2019-05-10** ou posterior para o tipo de recurso de implantação

Versões anteriores de PowerShell, CLI e REST API não suportam zero para contar.

Tenha cuidado utilizando a [implementação completa do modo](deployment-modes.md) com o laço de cópia. Se recolocar com o modo completo para um grupo de recursos, quaisquer recursos que não sejam especificados no modelo após a resolução do ciclo de cópia são eliminados.

## <a name="resource-iteration"></a>Iteração de recursos

O exemplo a seguir cria o número de contas de armazenamento especificadas no `storageCount` parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

Note que o nome de cada recurso inclui a `copyIndex()` função, que devolve a iteração atual no loop. `copyIndex()` é baseado em zero. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes:

- armazenamento0
- armazenamento1
- armazenamento2.

Para compensar o valor do índice, pode passar um valor na `copyIndex()` função. O número de iterações ainda está especificado no elemento de cópia, mas o valor `copyIndex` é compensado pelo valor especificado. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

- armazenamento1
- armazenamento2
- armazenamento3

A operação da cópia é útil quando se trabalha com matrizes porque pode iterar através de cada elemento da matriz. Utilize a `length` função na matriz para especificar a contagem de iterações e `copyIndex` para recuperar o índice atual na matriz.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Note que o índice `i` é usado na criação do nome de recurso da conta de armazenamento.

---

O exemplo a seguir cria uma conta de armazenamento para cada nome fornecido no parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Se pretender devolver os valores dos recursos implantados, pode utilizar [cópia na secção saídas](copy-outputs.md).

## <a name="serial-or-parallel"></a>Série ou Paralelo

Por predefinição, o Gestor de Recursos cria os recursos em paralelo. Não se aplica qualquer limite ao número de recursos mobilizados em paralelo, para além do limite total de 800 recursos no modelo. A ordem pela qual são criadas não é garantida.

No entanto, é possível que queira especificar que os recursos são implantados em sequência. Por exemplo, ao atualizar um ambiente de produção, é melhor escalonar as atualizações para que apenas um determinado número seja atualizado a qualquer momento.

Por exemplo, para implementar em série contas de armazenamento duas de cada vez, utilize:

# <a name="json"></a>[JSON](#tab/json)

Para implementar em série mais de uma instância de um recurso, definido `mode` para **série** e para o número `batchSize` de casos a implementar de cada vez. Com o modo de série, o Resource Manager cria uma dependência de instâncias anteriores no loop, pelo que não inicia um lote até que o lote anterior esteja concluído.

O valor para `batchSize` não pode exceder o valor para `count` o elemento de cópia.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

A `mode` propriedade também aceita **paralelo,** que é o valor padrão.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Para implementar em série mais de um caso de recurso, desloque o `batchSize` [decorador](./bicep-file.md#resource-and-module-decorators) para o número de casos a implementar de cada vez. Com o modo de série, o Resource Manager cria uma dependência de instâncias anteriores no loop, pelo que não inicia um lote até que o lote anterior esteja concluído.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Iteração para um recurso infantil

Não pode usar um ciclo de cópia para um recurso para crianças. Para criar mais de um exemplo de um recurso que normalmente define como aninhado dentro de outro recurso, deve, em vez disso, criar esse recurso como um recurso de alto nível. Define a relação com o recurso dos pais através das propriedades do tipo e nome.

Por exemplo, suponha que normalmente define um conjunto de dados como um recurso infantil dentro de uma fábrica de dados.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Para criar mais de um conjunto de dados, mova-o para fora da fábrica de dados. O conjunto de dados deve estar ao mesmo nível da fábrica de dados, mas ainda é um recurso infantil da fábrica de dados. Você preserva a relação entre conjunto de dados e fábrica de dados através das propriedades do tipo e nome. Uma vez que o tipo já não pode ser deduzido da sua posição no modelo, deve fornecer o tipo totalmente qualificado no formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Para estabelecer uma relação pai/filho com uma instância da fábrica de dados, forneça um nome para o conjunto de dados que inclui o nome do recurso principal. Utilize o formato: `{parent-resource-name}/{child-resource-name}`.

O exemplo a seguir mostra a implementação:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram cenários comuns para a criação de mais de um caso de recurso ou propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Armazenamento de cópias](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implementa mais de uma conta de armazenamento com um número de índice no nome. |
|[Armazenamento de cópias em série](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implementa várias contas de armazenamento uma de cada vez. O nome inclui o número do índice. |
|[Armazenamento de cópia com matriz](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implementa várias contas de armazenamento. O nome inclui um valor de uma matriz. |

## <a name="next-steps"></a>Passos seguintes

- Para definir dependências de recursos que são criados num ciclo de cópia, consulte [Definir a ordem para implantar recursos em modelos ARM](define-resource-dependency.md).
- Para passar por um tutorial, consulte [Tutorial: Crie múltiplas instâncias de recursos com modelos ARM](template-tutorial-create-multiple-instances.md).
- Para um módulo Microsoft Learn que cubra a cópia de recursos, consulte [Gerir implementações complexas em nuvem utilizando funcionalidades avançadas do modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
- Para outras utilizações do ciclo de cópia, consulte:
  - [Iteração de propriedade em modelos ARM](copy-properties.md)
  - [Iteração variável em modelos ARM](copy-variables.md)
  - [Iteração de saída em modelos ARM](copy-outputs.md)
- Para obter informações sobre a utilização de cópias com modelos aninhados, consulte [utilizar a cópia](linked-templates.md#using-copy).
