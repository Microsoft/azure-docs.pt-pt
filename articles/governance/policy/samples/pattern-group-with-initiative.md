---
title: 'Padrão: Definições políticas de grupo com iniciativas'
description: Este padrão de Política Azure fornece um exemplo de como agrupar definições políticas numa iniciativa
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172857"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Padrão de política azure: definições de política de grupo

Uma iniciativa é um grupo de definições políticas. Ao agrupar definições de políticas relacionadas num único objeto, pode criar uma única atribuição que teria sido múltiplas atribuições.

## <a name="sample-initiative-definition"></a>Definição de iniciativa de amostra

Esta iniciativa implementa duas definições políticas, cada uma das quais tem os parâmetros **tagName** e **tagValue.** A iniciativa em si tem dois parâmetros: **costCenterValue** e **productNameValue**.
Estes parâmetros de iniciativa são fornecidos a cada uma das definições políticas agrunadas. Este desenho maximiza a reutilização das definições políticas existentes, limitando ao mesmo tempo o número de atribuições criadas para as implementar conforme necessário.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Explicação

#### <a name="initiative-parameters"></a>Parâmetros de iniciativa

Uma iniciativa pode definir os seus próprios parâmetros que são depois passados para as definições políticas agrunadas.
Neste exemplo, tanto **o costCenterValue** como **o productNameValue** são definidos como parâmetros de iniciativa. Os valores são fornecidos quando a iniciativa é atribuída.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Inclui definições políticas

Cada definição de política incluída deve fornecer a **definição de definição** de política e um conjunto de **parâmetros** se a definição de política aceitar parâmetros. No snippet abaixo, a definição de política incluída leva dois parâmetros: **tagName** e **tagValue**. **TagName** é definido com um literal, mas **tagValue** utiliza o parâmetro **costCenterValue** definido pela iniciativa. Esta passagem de valores melhora a reutilização.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).