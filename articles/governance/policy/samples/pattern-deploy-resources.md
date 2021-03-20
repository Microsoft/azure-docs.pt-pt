---
title: 'Padrão: Mobilizar recursos com definição de política'
description: Este padrão de Política Azure fornece um exemplo de como implementar recursos com uma definição de política implantadaIfNotExists.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 0a9eec54954b8963f38b3f19a0d0cabffe1092e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89649980"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Padrão de Política Azure: mobilizar recursos

O efeito [implementarIfNotExists](../concepts/effects.md#deployifnotexists) permite implementar um [modelo de Gestor de Recursos Azure](../../../azure-resource-manager/templates/overview.md) (modelo ARM) ao criar ou atualizar um recurso que não esteja em conformidade. Esta abordagem pode ser preferível à utilização do efeito [de negação,](../concepts/effects.md#deny) uma vez que permite que os recursos continuem a ser criados, mas garante que as alterações são feitas para os tornar conformes.

## <a name="sample-policy-definition"></a>Definição de política de amostra

Esta definição de política utiliza o operador de **campo** para avaliar o `type` recurso criado ou atualizado. Quando este recurso é um _Microsoft.Network/virtualNetworks,_ a política procura um observador de rede na localização do novo recurso ou atualizado. Se um observador de rede correspondente não estiver localizado, o modelo ARM é implantado para criar o recurso em falta.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Explicação

#### <a name="existencecondition"></a>existênciaCondição

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

O bloco **properties.policyRule.then.details** diz à Azure Policy o que procurar relacionado com o recurso criado ou atualizado no **properties.policyRule.if** block. Neste exemplo, um observador de rede na rede de **recursosWatcherRG** deve existir com **um campo** igual `location` à localização do novo recurso ou atualizado. A `field()` utilização da função permite a **existênciaCondição** para aceder a propriedades no recurso novo ou atualizado, especificamente o `location` imóvel.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

O **papelDefinitionIds** _matriz_ propriedade no bloco **properties.policyRule.then.details** diz a definição de política que os direitos da identidade gerida precisam para implementar o modelo de Gestor de Recursos incluído. Esta propriedade deve ser definida para incluir papéis que tenham as permissões necessárias pela implementação do modelo, mas deve usar o conceito de "princípio de menor privilégio" e apenas ter as operações necessárias e nada mais.

#### <a name="deployment-template"></a>Modelo de implementação

A parte de **implantação** da definição de política tem um bloco **de propriedades** que define os três componentes principais:

- **modo** - Esta propriedade define o modo de [implementação](../../../azure-resource-manager/templates/deployment-modes.md) do modelo.

- **modelo** - Esta propriedade inclui o próprio modelo. Neste exemplo, o parâmetro do modelo de **localização** define a localização do novo recurso de observador de rede.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parâmetros** - Esta propriedade define parâmetros que são fornecidos ao **modelo**. Os nomes dos parâmetros devem corresponder ao que são definidos no **modelo.** Neste exemplo, o parâmetro é nomeado **local** para combinar. O valor da **localização** volta a utilizar `field()` a função para obter o valor do recurso avaliado, que é a rede virtual no **policyRule.if** block.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).