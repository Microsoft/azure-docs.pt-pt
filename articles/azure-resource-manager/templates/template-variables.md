---
title: Variáveis em modelos
description: Descreve como definir variáveis num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75483820"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variáveis no modelo do Azure Resource Manager

Este artigo descreve como definir e usar variáveis no seu modelo de Gestor de Recursos Azure. Usa variáveis para simplificar o seu modelo. Em vez de repetir expressões complicadas ao longo do seu modelo, define uma variável que contém a expressão complicada. Em seguida, você refere essa variável conforme necessário em todo o seu modelo.

O Gestor de Recursos resolve variáveis antes de iniciar as operações de implantação. Onde quer que a variável seja utilizada no modelo, o Resource Manager substitui-a pelo valor resolvido.

## <a name="define-variable"></a>Definir variável

O exemplo a seguir mostra uma definição variável. Cria um valor de cadeia para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro, e concatena-lo para uma corda única.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Não é possível utilizar a função [de referência](template-functions-resource.md#reference) ou qualquer das funções da [lista](template-functions-resource.md#list) na secção de variáveis. Estas funções obtêm o estado de funcionamento de um recurso, e não podem ser executadas antes da implementação quando as variáveis são resolvidas.

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

## <a name="configuration-variables"></a>Variáveis de configuração

Pode definir variáveis que possuam valores relacionados para configurar um ambiente. Define-se a variável como um objeto com os valores. O exemplo a seguir mostra um objeto que detém valores para dois ambientes - **teste** e **prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

Nos parâmetros, cria-se um valor que indica quais os valores de configuração a utilizar.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Para recuperar as definições para o ambiente especificado, utilize a variável e o parâmetro em conjunto.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos que se seguem demonstram cenários para a utilização de variáveis.

|Modelo  |Descrição  |
|---------|---------|
| [definições variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo não implementa recursos. Constrói valores variáveis e devolve esses valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define valores de configuração. O modelo não implementa recursos. Constrói valores variáveis e devolve esses valores. |
| [regras de segurança da rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e arquivo de [parâmetros](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Constrói um conjunto no formato correto para atribuir regras de segurança a um grupo de segurança de rede. |

## <a name="next-steps"></a>Passos seguintes

* Para conhecer as propriedades disponíveis para variáveis, consulte [a estrutura e sintaxe dos modelos Azure Resource Manager](template-syntax.md).
* Para recomendações sobre a criação de variáveis, consulte [as melhores práticas - variáveis.](template-best-practices.md#variables)
