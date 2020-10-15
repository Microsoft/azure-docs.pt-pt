---
title: 'Padrão: Propriedades de campo em uma definição de política'
description: Este padrão de Política Azure fornece um exemplo de como usar propriedades de campo numa definição de política.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 267c687f78f0bbb100843faee40ab6f3d3cbb64c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072973"
---
# <a name="azure-policy-pattern-field-properties"></a>Padrão de política azul: propriedades de campo

O operador de [campo](../concepts/definition-structure.md#fields) avalia a propriedade ou pseudónimo especificado [a um](../concepts/definition-structure.md#aliases) valor fornecido para uma determinada [condição](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Definição de política de amostra

Esta definição de política permite-lhe definir regiões permitidas que satisfaçam os requisitos de geolocalização da sua organização. Os recursos permitidos são definidos em lista de **parâmetrosOfAllowedLocations** _(matriz)._ Os recursos que correspondem à definição são [negados.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

O operador **de campo** é utilizado três vezes dentro do [operador lógico](../concepts/definition-structure.md#logical-operators) **allOf**.

- O primeiro uso avalia a `location` propriedade com a condição de **nãoIn** para o parâmetro **listOfAllowedLocations.** **não funciona** como espera uma _matriz_ e o parâmetro é uma _matriz_. Se `location` o recurso criado ou atualizado não estiver na lista aprovada, este elemento avalia-se a verdade.
- O segundo uso também avalia a `location` propriedade, mas usa a condição **notEquals** para ver se o recurso é _global._ Se `location` o recurso criado ou atualizado não for _global,_ este elemento avalia-se a verdade.
- A última utilização avalia a `type` propriedade e utiliza a condição **notEquals** para validar o tipo de recurso não é _Microsoft.AzureActiveDirectory/b2cDirectories_. Se não for, este elemento avalia a verdade.

Se as três declarações de condição no operador lógico **avaliarem** verdadeiramente, a criação ou atualização de recursos é bloqueada pela Azure Policy.

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).