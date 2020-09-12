---
title: Cortar planos
description: Explica o que são os aviões cortados e como usá-los
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: d5de8374f58eaf8dc83f54f05557b0a125191c34
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613712"
---
# <a name="cut-planes"></a>Cortar planos

Um *plano de corte* é uma característica visual que corta pixels de um lado de um plano virtual, revelando o interior das [malhas.](../../concepts/meshes.md)
A imagem abaixo demonstra o efeito. A esquerda mostra a malha original, à direita pode-se olhar para dentro da malha:

![Avião cortado](./media/cutplane-1.png)

## <a name="limitations"></a>Limitações

* Por enquanto, a Azure Remote Rendering suporta um **máximo de oito aviões de corte ativo**. Pode criar mais componentes de plano cortados, mas se tentar ativar mais simultaneamente, ignorará a ativação. Desative primeiro outros aviões se quiser mudar o componente que deve afetar a cena.
* Cada plano cortado afeta todos os objetos remotamente renderizados. Atualmente, não existe forma de excluir objetos específicos ou peças de malha.
* Os planos de corte são puramente uma característica visual, não afetam o resultado de [consultas espaciais.](spatial-queries.md) Se quiser lançar um raio numa malha aberta, pode ajustar o ponto de partida do raio para estar no plano de corte. Desta forma, o raio só pode atingir partes visíveis.

## <a name="performance-considerations"></a>Considerações de desempenho

Cada plano de corte ativo incorre num pequeno custo durante a renderização. Desative ou elimine os aviões cortados quando não são necessários.

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

## <a name="api-documentation"></a>Documentação da API

* [C# CutPlaneComponent classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [Classe C++ CutPlaneComponent](https://docs.microsoft.com/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Próximos passos

* [Renderização unilateral única](single-sided-rendering.md)
* [Consultas espaciais](spatial-queries.md)
