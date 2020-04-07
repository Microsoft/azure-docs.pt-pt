---
title: Renderização unilateral
description: Descreve definições de renderização unifaceda e casos de utilização
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682016"
---
# <a name="single-sided-rendering"></a>Renderização unilateral

A maioria dos renderizadores usam [o abate na face traseira](https://en.wikipedia.org/wiki/Back-face_culling) para melhorar o desempenho. No entanto, quando as meshes são cortadas com [aviões cortados,](cut-planes.md)os utilizadores muitas vezes olham para o lado de trás dos triângulos. Se esses triângulos forem abatidos, o resultado não parece convincente.

A forma de prevenir de forma fiável este problema é tornar os triângulos *de dupla face*. Como não usar o abate na face traseira tem implicações no desempenho, por padrão azure Remote Rendering apenas muda para renderização dupla para malhas que estão se cruzando com um plano cortado.

A definição *de renderização unilateral* permite-lhe personalizar este comportamento.

> [!CAUTION]
> A definição de renderização unilateral é uma característica experimental. Pode ser removido novamente no futuro. Por favor, não altere a definição padrão, a menos que resolva realmente um problema crítico na sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

A definição de renderização unilateral só tem um efeito para `opaqueMaterialDefaultSidedness` as `SingleSided`maltes que foram [convertidas](../../how-tos/conversion/configure-model-conversion.md) com a opção definida para . Por defeito, esta `DoubleSided`opção está definida para .

## <a name="single-sided-rendering-setting"></a>Definição de renderização unilateral

Existem três modos diferentes:

**Normal:** Neste modo, as meshes são sempre renderizadas à medida que são convertidas. Isso significa que as `opaqueMaterialDefaultSidedness` malshes convertidas com set para `SingleSided` serão sempre renderizadas com abate de costas ativado, mesmo quando cruzam um avião cortado.

**DynamicDoubleSiding:** Neste modo, quando um plano cortado cruza uma malha, é automaticamente trocado para renderização dupla. Este modo é o modo predefinido.

**AlwaysDoubleSided:** Força toda a geometria unilateral a ser sempre tornada duplamente unilateral. Este modo é maioritariamente exposto para que possa facilmente comparar o impacto de desempenho entre renderização unilateral e dupla.

A alteração das definições de renderização unifaceda pode ser feita da seguinte forma:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>Passos seguintes

* [Aviões cortados](cut-planes.md)
* [Configurar a conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)
