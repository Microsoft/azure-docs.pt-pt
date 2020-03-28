---
title: 'Padrão: Parâmetros numa definição de política'
description: Este padrão de Política Azure fornece um exemplo de como utilizar parâmetros numa definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172808"
---
# <a name="azure-policy-pattern-parameters"></a>Padrão de política azure: parâmetros

Uma definição de política pode ser dinâmica para reduzir o número de definições políticas que são necessárias através da utilização de [parâmetros.](../concepts/definition-structure.md#parameters) O parâmetro é definido durante a atribuição de políticas. Os parâmetros têm um conjunto de propriedades pré-definidas que descrevem o parâmetro e como é usado.

## <a name="sample-1-string-parameters"></a>Amostra 1: Parâmetros de corda

Esta definição de política utiliza dois parâmetros, **tagName** e **tagValue** para definir o que a atribuição de políticas procura em recursos. Este formato permite que a política seja utilizada para qualquer número de combinações de nome seletiva e valor de etiqueta, mas apenas manter uma definição de política única.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

Nesta parte da definição de política, o parâmetro **tagName** é definido como uma _corda_ e é fornecida uma descrição para a sua utilização.

O parâmetro é então utilizado na **políticaRule.se** bloquear para tornar a política dinâmica. Aqui, é usado para definir o campo que é avaliado, que é uma etiqueta com o valor do **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Amostra 2: Parâmetros de matriz

Esta definição de política utiliza um único parâmetro, **listaOfBandwidthinMbps,** para verificar se o recurso do Circuito de Rota Expresso configura a definição de largura de banda para um dos valores aprovados. Se não corresponder, a criação ou atualização do recurso é [negada.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

Nesta parte da definição de política, o parâmetro **da listaOfBandwidthinMbps** é definido como uma _matriz_ e é fornecida uma descrição para a sua utilização. Como _matriz,_ tem vários valores para combinar.

O parâmetro é então utilizado na **políticaRule.if** block. Como parâmetro de _matriz,_ deve ser utilizada uma[condição](../concepts/definition-structure.md#conditions)de _matriz_
 **dentro** ou **não.**
Aqui, é usado contra o **serviceProvider.bandwidthInMbps** como um dos valores definidos.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).