---
title: 'Padrão: Parâmetros numa definição de política'
description: Este padrão de Política Azure fornece um exemplo de como usar parâmetros numa definição de política.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 6d938cdf2f31d30932d3631e99254b7d833f2941
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545578"
---
# <a name="azure-policy-pattern-parameters"></a>Padrão de política azul: parâmetros

Uma definição de política pode ser dinâmica para reduzir o número de definições políticas que são necessárias através da utilização de [parâmetros.](../concepts/definition-structure.md#parameters) O parâmetro é definido durante a atribuição de políticas. Os parâmetros têm um conjunto de propriedades pré-definidas que descrevem o parâmetro e como é usado.

## <a name="sample-1-string-parameters"></a>Amostra 1: Parâmetros de corda

Esta definição de política utiliza dois parâmetros, **tagName** e **tagValue** para definir o que a atribuição de política procura em recursos. Este formato permite que a definição de política seja usada para qualquer número de combinações de etiquetas e valor de etiqueta, mas apenas manter uma definição de política única.

> [!NOTE]
> Para obter uma amostra de etiqueta que utilize **o modo** _All_ e trabalhe com um grupo de recursos, consulte [Padrão: Tags - Amostra #1](./pattern-tags.md#sample-1-parameterize-tags).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

Nesta parte da definição de política, o parâmetro **tagName** é definido como uma _cadeia_ e uma descrição é fornecida para a sua utilização.

O parâmetro é então utilizado na **apóliceRule.se** bloquear para tornar a política dinâmica. Aqui, é usado para definir o campo que é avaliado, que é uma etiqueta com o valor do **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Amostra 2: Parâmetros de matriz

Esta definição de política utiliza um único parâmetro, **listOfBandwidthinMbps,** para verificar se o recurso do Circuito de Rota Expresso configura a definição de largura de banda para um dos valores aprovados. Se não corresponder, a criação ou atualização do recurso é [negada](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

Nesta parte da definição de política, o parâmetro **listOfBandwidthinMbps** é definido como um _array_ e uma descrição é fornecida para a sua utilização. Como _matriz,_ tem múltiplos valores a condizer.

O parâmetro é então utilizado na **apóliceRule.se** bloquear. Como parâmetro _de matriz,_ deve ser utilizada uma condição de _matriz_ 
 [condition](../concepts/definition-structure.md#conditions) **dentro** ou **não.**
Aqui, é usado contra o **serviçoProvider.bandwidthInMbps** pseudónimo como um dos valores definidos.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Amostra 3: Efeito parametrizado

Uma forma comum de reutilizar as definições políticas é parametrizar o efeito em si. Este exemplo utiliza um único parâmetro, **efeito**. Parametrizar o efeito permite atribuir a mesma definição a diferentes âmbitos com diferentes efeitos.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Amostra 3: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

Nesta parte da definição de política, o parâmetro do **efeito** é definido como _string_. A definição de política define o valor padrão para uma atribuição à _auditoria_ e limita as outras opções para _desativar_ e _negar_.

O parâmetro é então utilizado na **apóliceRule.em seguida,** bloquear para o _efeito_.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).