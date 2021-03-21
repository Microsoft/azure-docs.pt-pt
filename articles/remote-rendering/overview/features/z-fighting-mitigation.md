---
title: Mitigação de combate Z
description: Descreve técnicas para mitigar artefactos de luta z
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a399565d62b20f62b72257bcb9f3beb2c910ac98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594066"
---
# <a name="z-fighting-mitigation"></a>Mitigação de combate Z

Quando duas superfícies se sobrepõem, não é claro qual deve ser renderizado em cima da outra. O resultado varia mesmo por pixel, resultando em artefactos dependentes da câmara. Consequentemente, quando a câmara ou a malha se movem, estes padrões cintilam visivelmente. Este artefacto *chama-se "z-fighting".* Para aplicações AR e VR, o problema é intensificado porque os dispositivos montados na cabeça movem-se sempre. Para evitar o desconforto do espectador, a funcionalidade de mitigação do combate z está disponível na Renderização Remota Azure.

## <a name="z-fighting-mitigation-modes"></a>Modos de mitigação de luta z

|Situação                        | Resultado                               |
|---------------------------------|:-------------------------------------|
|Luta z regular               |![Nenhuma precedência determinística entre quads vermelhos e verdes](./media/zfighting-0.png)|
|Mitigação de luta z habilitada    |![Quad vermelho tem precedência](./media/zfighting-1.png)|
|Destaque de tabuleiro de verificação ativado|![Preferência de alternância quad vermelho e verde no padrão de tabuleiro de xadrez](./media/zfighting-2.png)|

O seguinte código permite a mitigação do combate ao z:

```cs
void EnableZFightingMitigation(RenderingSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Connection.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Connection()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> A mitigação do combate ao Z é um cenário global que afeta todas as malhas renderizadas.

## <a name="reasons-for-z-fighting"></a>Razões para o combate z

A luta z acontece principalmente por duas razões:

1. quando as superfícies estão muito longe da câmara, a precisão dos seus valores de profundidade degrada-se e os valores tornam-se indistinguíveis
1. quando as superfícies de uma malha fisicamente se sobrepõem

O primeiro problema pode sempre acontecer e é difícil de eliminar. Se isso acontecer na sua aplicação, certifique-se de que a relação entre a distância *do plano próximo* e a distância do plano *distante* é tão baixa quanto prática. Por exemplo, um avião próximo à distância 0.01 e um avião distante à distância 1000 criará este problema muito mais cedo, do que ter o avião próximo a 0,1 e o avião distante à distância 20.

O segundo problema é um indicador para conteúdos mal autoriais. No mundo real, dois objetos não podem estar no mesmo lugar ao mesmo tempo. Dependendo da aplicação, os utilizadores podem querer saber se existem superfícies sobrepostas e onde estão. Por exemplo, uma cena CAD de um edifício que é a base para uma construção do mundo real, não deve conter cruzamentos de superfície fisicamente impossíveis. Para permitir uma inspeção visual, o modo de realce está disponível, que apresenta potencial luta z como um padrão de tabuleiro de xadrez animado.

## <a name="limitations"></a>Limitações

A mitigação proporcionada de luta z é um grande esforço. Não há garantias de que remova todos os combates z. Também automaticamente preferirá uma superfície em vez de outra. Assim, quando se tem superfícies demasiado próximas umas das outras, pode acontecer que a superfície "errada" acabe por cima. Um caso comum de problema é quando o texto e outros decalques são aplicados a uma superfície. Com a mitigação do combate z permitiu que estes detalhes pudessem facilmente desaparecer.

## <a name="performance-considerations"></a>Considerações de desempenho

* Permitir a mitigação do combate z incorre pouco a nenhuma sobrecarga de desempenho.
* Além disso, permitir a sobreposição de luta de z incorrerá numa sobrecarga de desempenho não trivial, embora possa variar dependendo da cena.

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection.ZFightingMitigationSettings propriedade](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.zfightingmitigationsettings)
* [C++ RenderingConnection::ZFightingMitigationSettings()](/cpp/api/remote-rendering/renderingconnection#zfightingmitigationsettings)

## <a name="next-steps"></a>Passos seguintes

* [Modos de composição](../../concepts/rendering-modes.md)
* [Reprojeção da última fase](late-stage-reprojection.md)