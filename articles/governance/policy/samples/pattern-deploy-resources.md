---
title: 'Padrão: Implementar recursos com uma definição de política'
description: Este padrão de Política Azure fornece um exemplo de como mobilizar recursos com uma definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172675"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Padrão de política azure: recursos de implantação

O efeito [deployIfNotExists](../concepts/effects.md#deployifnotexists) permite implementar um modelo de Gestor de [Recursos Azure](../../../azure-resource-manager/templates/overview.md) ao criar ou atualizar um recurso que não seja compatível. Esta abordagem pode ser preferível à utilização do efeito [de negação,](../concepts/effects.md#deny) uma vez que permite que os recursos continuem a ser criados, mas assegura que as alterações são feitas para os tornar conformes.

## <a name="sample-policy-definition"></a>Definição de política de amostras

Esta definição de política utiliza o operador de **campo** para avaliar a `type` de recursos criados ou atualizados. Quando esse recurso é um _Microsoft.Network/virtualNetworks,_ a política procura um observador de rede na localização do novo recurso ou atualizado. Se não estiver localizado um observador de rede correspondente, o modelo do Gestor de Recursos é implementado para criar o recurso em falta.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Explicação

#### <a name="existencecondition"></a>existênciaCondição

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

O bloco **properties.policyRule.then.details** diz à Política Azure o que procurar relacionado com o recurso criado ou atualizado nas **propriedades.policyRule.if** block. Neste exemplo, um observador de rede na rede de **recursos WatcherRG** deve existir com **campo** `location` igual à localização do novo recurso ou atualizado. A utilização da função `field()` permite à **existênciaCondition** aceder a propriedades no novo recurso ou atualizado, especificamente a propriedade `location`.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

A propriedade de _array_ **roleDefinitionIds** nas **propriedades.policyRule.then.details** block indica a definição de política quais os direitos que a identidade gerida precisa para implementar o modelo de Gestor de Recursos incluído. Este imóvel deve ser definido para incluir funções que tenham as permissões necessárias pela implantação do modelo, mas deve usar o conceito de "princípio de menor privilégio" e apenas ter as operações necessárias e nada mais.

#### <a name="deployment-template"></a>Modelo de implementação

A parte de **implantação** da definição de política tem um bloco de **propriedades** que define os três componentes principais:

- **modo** - Esta propriedade define o modo de [implementação](../../../azure-resource-manager/templates/deployment-modes.md) do modelo.

- **modelo** - Esta propriedade inclui o próprio modelo. Neste exemplo, o parâmetro do modelo de **localização** define a localização do novo recurso de observador de rede.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parâmetros** - Esta propriedade define parâmetros que são fornecidos ao **modelo**. Os nomes do parâmetro devem coincidir com o que são definidos no **modelo**. Neste exemplo, o parâmetro é nomeado **local** para combinar. O valor da **localização** utiliza novamente a função `field()` para obter o valor do recurso avaliado, que é a rede virtual na **políticaRule.if** block.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).