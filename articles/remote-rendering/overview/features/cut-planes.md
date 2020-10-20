---
title: Cortar planos
description: Explica o que são os aviões cortados e como usá-los
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7d8905fbdcfc03f2683698cca57ab6c066e77863
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205936"
---
# <a name="cut-planes"></a>Cortar planos

Um *plano de corte* é uma característica visual que corta pixels de um lado de um plano virtual, revelando o interior das [malhas.](../../concepts/meshes.md)
A imagem abaixo demonstra o efeito. A esquerda mostra a malha original, à direita pode-se olhar para dentro da malha:

![Avião cortado](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Adicione um avião cortado à cena criando um *CutPlaneComponent*. A [localização](../../concepts/entities.md)e orientação do avião é determinada pela entidade proprietária do componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Propriedades CutPlaneComponent

As seguintes propriedades são expostas num componente de plano cortado:

* `Enabled`: Pode desligar temporariamente os planos cortados desativando o componente. Aviões de corte desativado não incorrem em sobrecargas e também não contam contra o limite global do avião.

* `Normal`: Especifica qual a direção (+X,-X,+Y,-Y,+Z,-Z) como o plano normal. Esta direção é relativa à orientação da entidade proprietária. Mova e rode a entidade proprietária para uma colocação exata.

* `FadeColor``FadeLength`e:

  Se o valor alfa do *FadeColor* não for zero, os pixels perto do plano cortado desaparecerão em direção à parte RGB do FadeColor. A força do canal alfa determina se irá desaparecer completamente em direção à cor desvanecendo-se ou apenas parcialmente. *FadeLength* define sobre a distância que este desvanecimento terá lugar.

* `ObjectFilterMask`: Uma máscara de broca de filtro que determina qual a geometria afetada pelo plano de corte. Consulte o próximo parágrafo para obter informações detalhadas.

### <a name="selective-cut-planes"></a>Aviões de corte seletivo

É possível configurar aviões de corte individuais para que apenas afetem geometria específica. A imagem a seguir ilustra como esta configuração pode parecer na prática:

![Aviões de corte seletivo](./media/selective-cut-planes.png)

A filtragem funciona através da **comparação lógica** da máscara de bits entre uma máscara de um pouco no lado do plano cortado e uma segunda máscara que é definida na geometria. Se o resultado de uma operação lógica `AND` entre as máscaras não for zero, o plano de corte afetará a geometria.

* A máscara bit no componente do plano de corte é definida através da sua `ObjectFilterMask` propriedade
* A máscara bit em uma suberquásia de geometria é definida através do [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)

Exemplos:

| Corte máscara de filtro de avião | Máscara de filtro de geometria  | Resultado da lógica `AND` | Avião cortado afeta a geometria?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) == 1   | (0000 0001) == 1  | (0000 0001) == 1  | Yes |
| (1111 0000) == 240 | (0001 0001) == 17 | (0001 0000) == 16 | Yes |
| (0000 0001) == 1   | (0000 0010) == 2  | (0000 0000) == 0  | No |
| (0000 0011) == 3   | (0000 1000) == 8  | (0000 0000) == 0  | No |

>[!TIP]
> Definir um plano de corte `ObjectFilterMask` para 0 significa que não afetará qualquer geometria porque o resultado da lógica `AND` nunca pode ser nulo. O sistema de renderização não considerará esses aviões em primeiro lugar, então este é um método leve para desativar aviões de corte individuais. Estes aviões cortados também não contam contra o limite de 8 aviões ativos.

## <a name="limitations"></a>Limitações

* A Azure Remote Rendering suporta um **máximo de oito aviões de corte ativo**. Pode criar mais componentes de plano cortados, mas se tentar ativar mais simultaneamente, ignorará a ativação. Desative primeiro outros aviões se quiser mudar quais os componentes que devem afetar a cena.
* Os planos de corte são uma característica puramente visual, não afetam o resultado de [consultas espaciais.](spatial-queries.md) Se quiser lançar um raio numa malha aberta, pode ajustar o ponto de partida do raio para estar no plano de corte. Desta forma, o raio só pode atingir partes visíveis.

## <a name="performance-considerations"></a>Considerações de desempenho

Cada plano de corte ativo incorre num pequeno custo durante a renderização. Desative ou elimine os aviões cortados quando não são necessários.

## <a name="api-documentation"></a>Documentação da API

* [C# CutPlaneComponent classe](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [Classe C++ CutPlaneComponent](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Passos seguintes

* [Renderização unilateral única](single-sided-rendering.md)
* [Consultas espaciais](spatial-queries.md)