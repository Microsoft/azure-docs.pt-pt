---
title: O tamanho do trabalho excedeu o erro
description: Descreve como resolver erros quando o tamanho ou o modelo do trabalho são demasiado grandes.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889370"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Resolver erros para o tamanho do trabalho excedidos

Este artigo descreve como resolver os **erros de JobSizeException** e **DeploymentJobSizeExceededException.**

## <a name="symptom"></a>Sintoma

Ao implementar um modelo, recebe um erro indicando que a implementação excedeu os limites.

## <a name="cause"></a>Causa

Obtém-se este erro quando a implantação excede um dos limites permitidos. Normalmente, vê-se este erro quando o seu modelo ou o trabalho que executa a implementação é demasiado grande.

O trabalho de destacamento não pode exceder 1 MB. O trabalho inclui metadados sobre o pedido. Nos modelos grandes, os metadados combinados com o modelo podem exceder o tamanho permitido num trabalho.

O modelo não pode exceder 4 MB. O limite de 4-MB aplica-se ao estado final do modelo depois de ter sido expandido para definições de recursos que usam [cópia](copy-resources.md) para criar muitas instâncias. O estado final também inclui os valores resolvidos das variáveis e dos parâmetros.

Outros limites para o modelo são:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Ao utilizar os ciclos de cópia para implantar recursos, não utilize o nome do loop como dependência:

```json
dependsOn: [ "nicLoop" ]
```

Em vez disso, utilize a instância do recurso a partir do loop de que precisa de depender. Por exemplo:

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>Solução 1 - Modelo de simplificação

A sua primeira opção é simplificar o modelo. Esta opção funciona quando o seu modelo implementa vários tipos de recursos diferentes. Considere dividir o modelo em [modelos ligados.](linked-templates.md) Divida os seus tipos de recursos em grupos lógicos e adicione um modelo ligado para cada grupo. Por exemplo, se precisar de implementar muitos recursos de networking, pode mover esses recursos para um modelo ligado.

Pode definir outros recursos como dependentes do modelo ligado e [obter valores a partir da saída do modelo ligado](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Solução 2 - Reduzir o tamanho do nome

Tente encurtar o comprimento dos nomes que utiliza para [parâmetros,](template-parameters.md) [variáveis](template-variables.md)e [saídas](template-outputs.md). Quando estes valores são repetidos através de ciclos de cópia, um nome grande é multiplicado muitas vezes.