---
title: Variáveis em modelos
description: Descreve como definir variáveis num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874439"
---
# <a name="variables-in-arm-template"></a>Variáveis no modelo ARM

Este artigo descreve como definir e usar variáveis no seu modelo de Gestor de Recursos Azure (modelo ARM). Usa variáveis para simplificar o seu modelo. Em vez de repetir expressões complicadas ao longo do seu modelo, define uma variável que contém a expressão complicada. Em seguida, você refere essa variável conforme necessário em todo o seu modelo.

O Gestor de Recursos resolve variáveis antes de iniciar as operações de implantação. Onde quer que a variável seja utilizada no modelo, o Resource Manager substitui-a pelo valor resolvido.

## <a name="define-variable"></a>Definir variável

Ao definir uma variável, forneça um valor ou expressão de modelo que se resolva a um [tipo de dados](template-syntax.md#data-types). Pode utilizar o valor a partir de um parâmetro ou de outra variável ao construir a variável.

Pode utilizar [funções de modelo](template-functions.md) na declaração variável, mas não pode utilizar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista.](template-functions-resource.md#list) Estas funções obtêm o estado de funcionamento de um recurso, e não podem ser executadas antes da implementação quando as variáveis são resolvidas.

O exemplo a seguir mostra uma definição variável. Cria um valor de cadeia para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro, e concatena-lo para uma corda única.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>Utilizar variável

No modelo, refere-se o valor do parâmetro utilizando a função [de variáveis.](template-functions-deployment.md#variables) O exemplo a seguir mostra como usar a variável para uma propriedade de recurso.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Modelo de exemplo

O modelo a seguir não implementa quaisquer recursos. Só mostra algumas formas de declarar variáveis.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Variáveis de configuração

Pode definir variáveis que possuam valores relacionados para configurar um ambiente. Define-se a variável como um objeto com os valores. O exemplo a seguir mostra um objeto que detém valores para dois ambientes - **teste** e **prod**. Passa-se num destes valores durante a implantação.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Próximos passos

* Para conhecer as propriedades disponíveis para variáveis, consulte [a estrutura e sintaxe dos modelos ARM.](template-syntax.md)
* Para recomendações sobre a criação de variáveis, consulte [as melhores práticas - variáveis.](template-best-practices.md#variables)
* Para um modelo de exemplo que atribui regras de segurança a um grupo de segurança de rede, consulte [as regras de segurança da rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e o ficheiro de [parâmetros](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
