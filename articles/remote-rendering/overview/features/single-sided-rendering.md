---
title: Composição unilateral
description: Descreve definições de renderização unifaceda e casos de utilização
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 1a9f80166e47b17644b37d4bc9b93e1abefe3432
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022765"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided":::renderização

A maioria dos renderizadores usam [o abate na face traseira](https://en.wikipedia.org/wiki/Back-face_culling) para melhorar o desempenho. No entanto, quando as meshes são cortadas com [aviões cortados,](cut-planes.md)os utilizadores muitas vezes olham para o lado de trás dos triângulos. Se esses triângulos forem abatidos, o resultado não parece convincente.

A forma de prevenir de forma fiável este problema é tornar os triângulos *de dupla face*. Como não usar o abate na face traseira tem implicações no desempenho, por padrão azure Remote Rendering apenas muda para renderização dupla para malhas que estão se cruzando com um plano cortado.

A definição de * :::no-loc text="single-sided"::: renderização* permite-lhe personalizar este comportamento.

> [!CAUTION]
> A :::no-loc text="single-sided"::: definição de renderização é uma característica experimental. Pode ser removido novamente no futuro. Por favor, não altere a definição padrão, a menos que resolva realmente um problema crítico na sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

A definição de :::no-loc text="single-sided"::: renderização só tem um efeito para as mhas que foram [convertidas](../../how-tos/conversion/configure-model-conversion.md) com a `opaqueMaterialDefaultSidedness` opção definida para `SingleSided` . Por defeito, esta opção está definida para `DoubleSided` .

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided":::definição de renderização

Existem três modos diferentes:

**Normal:** Neste modo, as meshes são sempre renderizadas à medida que são convertidas. Isso significa que as malshes convertidas com `opaqueMaterialDefaultSidedness` set para `SingleSided` serão sempre renderizadas com abate de costas ativado, mesmo quando cruzam um avião cortado.

**DynamicDoubleSiding:** Neste modo, quando um plano cortado cruza uma malha, é automaticamente trocado para renderização dupla. Este modo é o modo predefinido.

**AlwaysDoubleSided:** Força toda a geometria unilateral a ser sempre tornada duplamente unilateral. Este modo é maioritariamente exposto para que possa facilmente comparar o impacto de desempenho entre :::no-loc text="single-sided"::: e :::no-loc text="double-sided"::: renderização.

A alteração das definições de :::no-loc text="single-sided"::: renderização pode ser feita da seguinte forma:

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>Próximos passos

* [Cortar planos](cut-planes.md)
* [Configurar a conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)
