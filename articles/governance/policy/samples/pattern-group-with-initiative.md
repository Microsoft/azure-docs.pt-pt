---
title: 'Padrão: Definições de política de grupo com iniciativas'
description: Este padrão de Política Azure fornece um exemplo de como agrupar as definições políticas numa iniciativa.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: 7bbb2efdd27ead942fa0ef48f7785eec8bce9378
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092862"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Padrão de política Azure: definições de política de grupo

Uma iniciativa é um grupo de definições políticas. Ao agrupar definições de política relacionadas num único objeto, pode criar uma única atribuição que teria sido várias atribuições.

## <a name="sample-initiative-definition"></a>Definição de iniciativa de amostra

Esta iniciativa implementa duas definições políticas, cada uma das quais leva os parâmetros **tagName** e **tagValue.** A iniciativa em si tem dois parâmetros: **costCenterValue** e **produtoNameValue.**
Estes parâmetros de iniciativa são fornecidos a cada uma das definições políticas agrupadas. Este desenho maximiza a reutilização das definições políticas existentes, limitando ao mesmo tempo o número de atribuições criadas para as implementar conforme necessário.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Explicação

#### <a name="initiative-parameters"></a>Parâmetros de iniciativa

Uma iniciativa pode definir os seus próprios parâmetros que são depois passados para as definições políticas agrupadas.
Neste exemplo, tanto **o costCenterValue** como **o produtoNameValue** são definidos como parâmetros de iniciativa. Os valores são fornecidos quando a iniciativa é atribuída.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Inclui definições de política

Cada definição de política incluída deve fornecer a **políticaDefinitionId** e um conjunto de **parâmetros** se a definição de política aceitar parâmetros. No snippet abaixo, a definição de política incluída requer dois parâmetros: **tagName** e **tagValue**. **tagName** é definido com um literal, mas **tagValue** usa o **parâmetro costCenterValue** definido pela iniciativa. Esta passagem de valores melhora a reutilização.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).