---
title: Limites de objetos
description: Explica como os limites de objetos espaciais podem ser consultados
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681717"
---
# <a name="object-bounds"></a>Limites de objetos

Os limites dos objetos representam o volume que uma [entidade](entities.md) e os seus filhos ocupam. Na renderização remota Azure, os limites dos objetos são sempre dados como caixas de *delimitação alinhadas* com o eixo (AABB). Os limites dos objetos podem ser no *espaço local* ou no *espaço mundial.* De qualquer forma, estão sempre alinhados com o eixo, o que significa que as extensões e o volume podem diferir entre a representação espacial local e mundial.

## <a name="querying-object-bounds"></a>Limites de objeto seletiva

O AABB local de uma [malha](meshes.md) pode ser consultado diretamente a partir do recurso de malha. Estes limites podem ser transformados no espaço local ou no espaço mundial de uma entidade que usa a transformação da entidade.

É possível calcular os limites de toda uma hierarquia de objetos desta forma, mas isso requer atravessar a hierarquia, consultar os limites para cada malha, e combiná-los manualmente. Esta operação é aborrecida e ineficiente.

Uma maneira melhor `QueryLocalBoundsAsync` é `QueryWorldBoundsAsync` ligar ou numa entidade. A computação é então descarregada para o servidor e devolvida com o mínimo de atraso.

``` cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

## <a name="next-steps"></a>Passos seguintes

* [Consultas espaciais](../overview/features/spatial-queries.md)
