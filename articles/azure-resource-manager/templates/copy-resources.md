---
title: Implementar múltiplas instâncias de recursos
description: Utilize a operação de cópia e as matrizes num modelo de Gestor de Recursos Azure para implementar o tipo de recursos muitas vezes.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153323"
---
# <a name="resource-iteration-in-arm-templates"></a>Iteração de recursos em modelos ARM

Este artigo mostra-lhe como criar mais do que uma instância de um recurso no seu modelo de Gestor de Recursos Azure (ARM). Ao adicionar o elemento **de cópia** à secção de recursos do seu modelo, pode configurar dinamicamente o número de recursos para implantar. Também evita ter que repetir a sintaxe do modelo.

Também pode utilizar cópias com [propriedades,](copy-properties.md) [variáveis](copy-variables.md) e [saídas.](copy-outputs.md)

Se precisar especificar se um recurso está implantado, consulte o [elemento condição](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Iteração de recursos

O elemento de cópia tem o seguinte formato geral:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A propriedade **do nome** é qualquer valor que identifique o loop. A propriedade **de contagem** especifica o número de iterações que deseja para o tipo de recurso.

Utilize as propriedades do **modo** e do **loteSize** para especificar se os recursos são implantados em paralelo ou em sequência. Estas propriedades são descritas em [Série ou Paralelo.](#serial-or-parallel)

O exemplo seguinte cria o número de contas de armazenamento especificadas no parâmetro **do Depósito de Armazenamento.**

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
    ],
    "outputs": {}
}
```

Note que o nome `copyIndex()` de cada recurso inclui a função, que devolve a iteração atual no loop. `copyIndex()` é baseado em zero. Então, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes:

* armazenamento0
* armazenamento1
* armazenamento2.

Para deslocar o valor de índice, pode passar um valor na função copyIndex(). O número de iterações ainda é especificado no elemento de cópia, mas o valor do copyIndex é compensado pelo valor especificado. Então, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

* armazenamento1
* armazenamento2
* armazenamento3

A operação de cópia é útil quando se trabalha com matrizes porque pode iterar através de cada elemento da matriz. Utilize `length` a função na matriz para especificar `copyIndex` a contagem para iterações e para recuperar o índice atual na matriz.

O exemplo seguinte cria uma conta de armazenamento para cada nome fornecido no parâmetro.

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

Se pretender devolver valores dos recursos implantados, pode utilizar [cópiana secção de saídas](copy-outputs.md).

## <a name="serial-or-parallel"></a>Série ou Paralelo

Por padrão, o Gestor de Recursos cria os recursos em paralelo. Não aplica limites ao número de recursos utilizados paralelamente, com a lém do limite total de 800 recursos no modelo. A ordem em que são criadas não está garantida.

No entanto, pode querer especificar que os recursos são implantados em sequência. Por exemplo, ao atualizar um ambiente de produção, é possível que queira escalonar as atualizações para que apenas um determinado número seja atualizado a qualquer momento. Para implantar em série mais de uma `mode` instância `batchSize` de um recurso, definido para **série** e para o número de instâncias a implantar de cada vez. Com o modo de série, o Gestor de Recursos cria uma dependência de instâncias anteriores no loop, por isso não inicia um lote até que o lote anterior esteja concluído.

Por exemplo, para implantar em série contas de armazenamento duas de cada vez, use:

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

A propriedade do modo também aceita **paralelo,** que é o valor padrão.

## <a name="depend-on-resources-in-a-loop"></a>Depender dos recursos em loop

Especifica que um recurso é implantado após `dependsOn` outro recurso utilizando o elemento. Para implantar um recurso que dependa da recolha de recursos em loop, forneça o nome do loop de cópia no elemento dependsOn. O exemplo seguinte mostra como implementar três contas de armazenamento antes de implantar a máquina virtual. A definição completa da máquina virtual não é mostrada. Note que o elemento `storagecopy` de cópia tem o nome definido e `storagecopy`o elemento dependsOn para a máquina virtual também está definido para .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
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
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iteração para um recurso infantil

Não pode usar um ciclo de cópia para um recurso infantil. Para criar mais do que uma instância de um recurso que tipicamente define como aninhado dentro de outro recurso, deve, em vez disso, criar esse recurso como um recurso de alto nível. Define a relação com o recurso-mãe através do tipo e propriedades do nome.

Por exemplo, suponha que tipicamente define um conjunto de dados como um recurso infantil dentro de uma fábrica de dados.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
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

Para criar mais do que um conjunto de dados, mova-o para fora da fábrica de dados. O conjunto de dados deve estar ao mesmo nível da fábrica de dados, mas ainda é um recurso infantil da fábrica de dados. Preserva a relação entre o conjunto de dados e a fábrica de dados através das propriedades do tipo e do nome. Uma vez que o tipo já não pode ser inferido da sua posição `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`no modelo, deve fornecer o tipo totalmente qualificado no formato: .

Para estabelecer uma relação pai/filho com uma instância da fábrica de dados, forneça um nome para o conjunto de dados que inclua o nome do recurso-mãe. Utilize o formato: `{parent-resource-name}/{child-resource-name}`.

O exemplo que se segue mostra a implementação:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
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

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Se implementar um modelo com O Azure PowerShell 2.6 ou mais tarde, Azure CLI 2.0.74 ou mais tarde, ou rest API versão **2019-05-10** ou mais tarde, pode definir a contagem para zero. Versões anteriores de PowerShell, CLI e rest API não suportam zero para contagem.

Tenha cuidado ao utilizar a [implementação completa](deployment-modes.md) do modo com cópia. Se recolocar com o modo completo para um grupo de recursos, quaisquer recursos que não estejam especificados no modelo após a resolução do ciclo de cópia sou eliminado.

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos seguintes mostram cenários comuns para a criação de mais do que um recurso ou propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Armazenamento de cópias](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implementa mais de uma conta de armazenamento com um número de índice no nome. |
|[Armazenamento de cópia em série](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implementa várias contas de armazenamento uma de cada vez. O nome inclui o número do índice. |
|[Armazenamento de cópias com matriz](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implementa várias contas de armazenamento. O nome inclui um valor de uma matriz. |
|[Implantação vm com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementa vários discos de dados com uma máquina virtual. |
|[Múltiplas regras de segurança](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementa várias regras de segurança para um grupo de segurança de rede. Constrói as regras de segurança a partir de um parâmetro. Para o parâmetro, consulte [vários ficheiros de parâmetros NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie múltiplas instâncias](template-tutorial-create-multiple-instances.md)de recursos utilizando modelos ARM .
* Para outras utilizações do elemento cópia, consulte:
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Para obter informações sobre a utilização da cópia com modelos aninhados, consulte [A Utilização da Cópia](linked-templates.md#using-copy).
* Se quiser saber sobre as secções de um modelo, consulte [os modelos ARM autores](template-syntax.md).
* Para aprender a implementar o seu modelo, consulte [implementar uma aplicação com modelo ARM](deploy-powershell.md).

