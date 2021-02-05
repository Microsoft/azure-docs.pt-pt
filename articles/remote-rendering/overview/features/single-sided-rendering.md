---
title: Composição unilateral
description: Descreve configurações de renderização unilaterais e casos de utilização
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: fea9deae3948b36732b5ea5203fceea6bec07fb9
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594083"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: renderização

A maioria dos renderizadores usam [o abate da cara](https://en.wikipedia.org/wiki/Back-face_culling) de trás para melhorar o desempenho. No entanto, quando as malhas são cortadas com [planos cortados,](cut-planes.md)os utilizadores muitas vezes olham para o lado de trás dos triângulos. Se esses triângulos forem abatidos, o resultado não parece convincente.

A forma de prevenir este problema de forma fiável é tornar os triângulos *de dupla unilateralidade.* Como não utilizar o abate na face traseira tem implicações de desempenho, por defeito a renderização remota do Azure apenas muda para renderização dupla para malhas que estão se cruzando com um plano cortado.

A *:::no-loc text="single-sided"::: definição de renderização* permite-lhe personalizar este comportamento.

> [!CAUTION]
> A :::no-loc text="single-sided"::: definição de renderização é uma característica experimental. Pode ser removido de novo no futuro. Por favor, não altere a definição predefinição, a menos que realmente resolva um problema crítico na sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

A :::no-loc text="single-sided"::: definição de renderização só tem um efeito para as malhas que foram [convertidas](../../how-tos/conversion/configure-model-conversion.md) com a `opaqueMaterialDefaultSidedness` opção definida para `SingleSided` . Por predefinição, esta opção está definida para `DoubleSided` .

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: definição de renderização

Existem três modos diferentes:

**Normal:** Neste modo, as malhas são sempre renderizadas à medida que são convertidas. Isto significa que as malhas convertidas com `opaqueMaterialDefaultSidedness` conjunto `SingleSided` serão sempre renderizadas com o abate na face traseira, mesmo quando cruzam um plano de corte.

**DynamicDoubleSiding:** Neste modo, quando um plano de corte intersecta uma malha, é automaticamente comutado para renderização dupla. Este modo é o modo predefinido.

**SempreDoubleSided:** Força toda a geometria unilateral a ser sempre transformada em dupla unilateralidade. Este modo é maioritariamente exposto para que possa comparar facilmente o impacto de desempenho entre :::no-loc text="single-sided"::: e :::no-loc text="double-sided"::: renderização.

A alteração das :::no-loc text="single-sided"::: definições de renderização pode ser feita da seguinte forma:

```cs
void ChangeSingleSidedRendering(RenderingSession session)
{
    SingleSidedSettings settings = session.Connection.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<RenderingSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Connection()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection.SingleSidedSettings propriedade](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.singlesidedsettings)
* [C++ RenderingConnection::SingleSidedSettings()](/cpp/api/remote-rendering/renderingconnection#singlesidedsettings)

## <a name="next-steps"></a>Passos seguintes

* [Cortar planos](cut-planes.md)
* [Configurar a conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)