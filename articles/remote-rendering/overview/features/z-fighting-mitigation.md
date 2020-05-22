---
title: Mitigação de combate Z
description: Descreve técnicas para mitigar artefactos de luta z
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 69774c0014aac26c7266620bbe7d06ba37d6023b
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758814"
---
# <a name="z-fighting-mitigation"></a>Mitigação de combate Z

Quando duas superfícies se sobrepõem, não é claro qual deve ser renderizado em cima do outro. O resultado varia mesmo por pixel, resultando em artefactos dependentes da vista. Consequentemente, quando a câmara ou a malha se movem, estes padrões cintilam visivelmente. Este artefacto *chama-se z-fighting.* Para aplicações AR e VR, o problema intensifica-se porque os dispositivos montados na cabeça movem-se naturalmente. Para evitar o desconforto do espectador, a funcionalidade de mitigação do z-fighting está disponível na Renderização Remota Azure.

## <a name="z-fighting-mitigation-modes"></a>Modos de mitigação de luta Z

|Situação                        | Resultado                               |
|---------------------------------|:-------------------------------------|
|Luta de z regular               |![Luta Z](./media/zfighting-0.png)|
|Mitigação de luta Z habilitada    |![Luta Z](./media/zfighting-1.png)|
|Realçação de checkerboard habilitado|![Luta Z](./media/zfighting-2.png)|

O seguinte código permite a mitigação do combate ao z:

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = *session->Actions()->ZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->Enabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->Highlighting(highlight);
}
```


> [!NOTE]
> A mitigação do combate ao Z é um cenário global que afeta todas as malhas renderizadas.

## <a name="reasons-for-z-fighting"></a>Razões para a luta z

A luta por Z acontece principalmente por duas razões:

1. quando as superfícies estão muito longe da câmara, a precisão dos seus valores de profundidade degrada-se e os valores tornam-se indistinguíveis
1. quando as superfícies em uma malha fisicamente sobrepõem-se

O primeiro problema pode sempre acontecer e é difícil de eliminar. Se isso acontecer na sua aplicação, certifique-se de que a relação entre a distância próxima do *avião* até à distância *do avião distante* é tão baixa quanto prática. Por exemplo, um avião próximo à distância 0.01 e um avião distante à distância 1000 criará este problema muito mais cedo, do que ter o avião próximo a 0,1 e o avião distante à distância 20.

O segundo problema é um indicador para conteúdos mal-autorizados. No mundo real, dois objetos não podem estar no mesmo lugar ao mesmo tempo. Dependendo da aplicação, os utilizadores podem querer saber se existem superfícies sobrepostas e onde estão. Por exemplo, uma cena CAD de um edifício que é a base para uma construção no mundo real, não deve conter intersecções de superfície fisicamente impossíveis. Para permitir a inspeção visual, o modo de destaque está disponível, que apresenta potenciais lutas de z como um padrão de tabuleiro de xadrez animado.

## <a name="limitations"></a>Limitações

A mitigação do z-fighting fornecida é um esforço melhor. Não há garantias de que remova todos os combates. Também prefere automaticamente uma superfície em vez da outra. Assim, quando se tem superfícies demasiado próximas umas das outras, pode acontecer que a superfície "errada" acabe por cima. Um caso comum de problema é quando o texto e outros decalques são aplicados à superfície. Com a mitigação do combate z permitiu que estes detalhes pudessem facilmente desaparecer.

## <a name="performance-considerations"></a>Considerações de desempenho

* Permitir a mitigação do z-fighting incorre pouco a nenhuma sobrecarga de desempenho.
* Além disso, permitir a sobreposição de luta z incorre numa sobrecarga de desempenho não trivial, embora possa variar dependendo da cena.

## <a name="next-steps"></a>Passos seguintes

* [Modos de composição](../../concepts/rendering-modes.md)
* [Reprojeção da última fase](late-stage-reprojection.md)
