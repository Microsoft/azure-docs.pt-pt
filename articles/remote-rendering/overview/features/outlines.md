---
title: Composição de contorno
description: Explica como fazer a renderização do esboço de seleção
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c04f2312926d3b6d668dff712eedb57d816c8bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592012"
---
# <a name="outline-rendering"></a>Composição de contorno

Os objetos selecionados podem ser destacados visualmente adicionando a renderização de contornos através do [componente de sobreposição de estado hierárquico](../../overview/features/override-hierarchical-state.md). Este capítulo explica como os parâmetros globais para a renderização de contornos são alterados através da API do cliente.

As propriedades de contorno são um cenário global. Todos os objetos que utilizam a renderização de contornos utilizarão a mesma definição - não é possível utilizar uma cor de contorno por objeto.

## <a name="parameters-for-outlinesettings"></a>Parâmetros para `OutlineSettings`

A classe `OutlineSettings` detém as configurações relacionadas com as propriedades de contorno global. Expõe os seguintes membros:

| Parâmetro      | Tipo    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A cor que é usada para desenhar o contorno. A porção alfa é ignorada.         |
| `PulseRateHz`    | float   | A taxa a que o contorno oscila por segundo|
| `PulseIntensity` | float   | A intensidade do efeito de pulso de contorno. Deve estar entre 0,0 para não pulsar e 1.0 para pulsação total. A intensidade define implicitamente a opacidade mínima do contorno como `MinOpacity = 1.0 - PulseIntensity` . |

![Um objeto renderizado três vezes com parâmetros de contorno diferentes ](./media/outlines.png) O efeito de alterar o parâmetro de amarelo `color` (à esquerda) para magenta (centro) e `pulseIntensity` de 0 a 0,8 (direita).

## <a name="example"></a>Exemplo

O código que se segue mostra um exemplo para definir parâmetros de contorno através da API:

```cs
void SetOutlineParameters(RenderingSession session)
{
    OutlineSettings outlineSettings = session.Connection.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<RenderingSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Connection()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Desempenho

A renderização do esboço pode ter um impacto significativo no desempenho da renderização. Este impacto varia em função da relação espacial espaço-ecrã entre objetos selecionados e não selecionados para um dado quadro.

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection.OutlineSettings propriedade](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.outlinesettings)
* [C++ RenderingConnection::OutlineSettings()](/cpp/api/remote-rendering/renderingconnection#outlinesettings)

## <a name="next-steps"></a>Passos seguintes

* [Componente hierárquico de sobreposição de estado](../../overview/features/override-hierarchical-state.md)