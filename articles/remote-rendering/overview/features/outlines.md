---
title: Composição de contorno
description: Explica como fazer a renderização do contorno de seleção
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4f3889a0ba121cb9a3167c1f6ac95f0bed280539
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759018"
---
# <a name="outline-rendering"></a>Composição de contorno

Os objetos selecionados podem ser realçados visualmente adicionando contornos através do [componente hierárquico](../../overview/features/override-hierarchical-state.md)de sobreposição do estado . Este capítulo explica como os parâmetros globais para a renderização de contornos são alterados através da API cliente.

As propriedades de contorno são um cenário global. Todos os objetos que utilizam a renderização de contorno usarão a mesma definição - não é possível utilizar uma cor de contorno por objeto.

## <a name="parameters-for-outlinesettings"></a>Parâmetros para`OutlineSettings`

A classe `OutlineSettings` detém as configurações relacionadas com propriedades de contorno global. Expõe os seguintes membros:

| Parâmetro      | Tipo    | Descrição                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A cor que é usada para desenhar o contorno. A porção alfa é ignorada.         |
| `PulseRateHz`    | float   | A taxa a que o contorno oscila por segundo|
| `PulseIntensity` | float   | A intensidade do efeito de pulso de contorno. Deve estar entre 0,0 sem pulsação e 1,0 para pulsar. A intensidade define implicitamente a opacidade mínima do contorno como `MinOpacity = 1.0 - PulseIntensity` . |

![Contornos ](./media/outlines.png) O efeito de mudar o parâmetro do amarelo `color` (esquerdo) para magenta (centro) e `pulseIntensity` de 0 para 0,8 (direita).

## <a name="example"></a>Exemplo

O seguinte código mostra um exemplo para definir parâmetros de contorno através da API:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = *session->Actions()->OutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->Color(outlineColor);
    outlineSettings->PulseRateHz(2.0f);
    outlineSettings->PulseIntensity(0.5f);
}
```

## <a name="performance"></a>Desempenho

A renderização de contornos pode ter um impacto significativo no desempenho da renderização. Este impacto varia com base na relação espacial do espaço de ecrã entre objetos selecionados e não selecionados para um determinado quadro.

## <a name="next-steps"></a>Passos seguintes

* [Componente hierárquico de sobreposição do estado](../../overview/features/override-hierarchical-state.md)
