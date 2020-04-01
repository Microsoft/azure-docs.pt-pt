---
title: 'Padrão: Propriedades de campo em uma definição de política'
description: Este padrão de Política Azure fornece um exemplo de como usar propriedades de campo numa definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172864"
---
# <a name="azure-policy-pattern-field-properties"></a>Padrão de política azure: propriedades de campo

O operador de [campo](../concepts/definition-structure.md#fields) avalia o imóvel ou [pseudónimo](../concepts/definition-structure.md#aliases) especificado a um valor fornecido para uma determinada [condição](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Definição de política de amostras

Esta definição de política permite-lhe definir regiões permitidas que satisfaçam os requisitos de geolocalização da sua organização. Os recursos permitidos são definidos na lista de **parâmetrosOfAllowedLocations** _(matriz)._ Os recursos que correspondem à definição são [negados.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

O operador de **campo** é utilizado três vezes dentro do [operador lógico](../concepts/definition-structure.md#logical-operators) **.**

- A primeira utilização `location` avalia a propriedade com a condição **não Em** condições para a **listaDeLocais Permitidos.** **notfunciona** como espera uma _matriz_ e o parâmetro é uma _matriz_. Se `location` o recurso criado ou atualizado não estiver na lista aprovada, este elemento avalia a verdade.
- O segundo uso também `location` avalia a propriedade, mas usa a condição **não Igualpara** ver se o recurso é _global_. Se `location` o recurso criado ou atualizado não for _global,_ este elemento avalia a verdade.
- A última utilização `type` avalia a propriedade e utiliza a condição **notEquals** para validar o tipo de recurso não é _Microsoft.AzureActiveDirectory/b2cDirectories_. Se não for, este elemento avalia a verdade.

Se as três declarações de condição no **allOf** logic operator avaliar em verdade, a criação de recursos ou atualização é bloqueada pela Política Azure.

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).