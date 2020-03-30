---
title: Variáveis em modelos
description: Descreve como definir variáveis num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483820"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variáveis no modelo de Gestor de Recursos Azure

Este artigo descreve como definir e usar variáveis no seu modelo de Gestor de Recursos Azure. Usa variáveis para simplificar o seu modelo. Em vez de repetir expressões complicadas em todo o seu modelo, você define uma variável que contém a expressão complicada. Em seguida, você refere essa variável como necessário em todo o seu modelo.

O Gestor de Recursos resolve variáveis antes de iniciar as operações de implantação. Onde quer que a variável seja utilizada no modelo, o Gestor de Recursos substitui-a pelo valor resolvido.

## <a name="define-variable"></a>Definir variável

O exemplo que se segue mostra uma definição variável. Cria um valor de cadeia para um nome de conta de armazenamento. Usa várias funções de modelo para obter um valor de parâmetro, e concatena-o a uma corda única.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Não pode utilizar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista](template-functions-resource.md#list) na secção variáveis. Estas funções obtêm o estado de execução de um recurso, e não podem ser executadas antes da implementação quando as variáveis são resolvidas.

## <a name="use-variable"></a>Usar variável

No modelo, referencia-se o valor do parâmetro utilizando a função [variáveis.](template-functions-deployment.md#variables) O exemplo que se segue mostra como usar a variável para uma propriedade de recursos.

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

Pode definir variáveis que possuam valores relacionados para configurar um ambiente. Define a variável como um objeto com os valores. O exemplo que se segue mostra um objeto que detém valores para dois ambientes - **teste** e **prod**.

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
| [definições variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo não distribui recursos. Constrói valores variáveis e devolve esses valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define valores de configuração. O modelo não distribui recursos. Constrói valores variáveis e devolve esses valores. |
| [regras de segurança da rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e arquivo [parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Constrói uma matriz no formato correto para atribuir regras de segurança a um grupo de segurança de rede. |

## <a name="next-steps"></a>Passos seguintes

* Para conhecer as propriedades disponíveis para variáveis, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure.](template-syntax.md)
* Para recomendações sobre a criação de variáveis, consulte [As melhores práticas - variáveis](template-best-practices.md#variables).
