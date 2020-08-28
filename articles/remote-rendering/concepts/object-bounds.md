---
title: Limites dos objetos
description: Explica como os limites dos objetos espaciais podem ser questionados
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 099b0175b906e48034e663b575517e49bce7e753
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021855"
---
# <a name="object-bounds"></a>Limites dos objetos

Os limites dos objetos representam o volume que uma [entidade](entities.md) e os seus filhos ocupam. Na renderização remota de Azure, os limites dos objetos são sempre dados como *caixas de delimitação alinhadas com o eixo* (AABB). Os limites dos objetos podem ser no *espaço local* ou no *espaço mundial.* De qualquer forma, estão sempre alinhados com eixos, o que significa que as extensões e o volume podem diferir entre a representação espacial local e mundial.

## <a name="querying-object-bounds"></a>Consulta limites de objeto

O AABB local de uma [malha](meshes.md) pode ser consultado diretamente a partir do recurso de malha. Estes limites podem ser transformados no espaço local ou no espaço mundial de uma entidade usando a transformação da entidade.

É possível calcular os limites de toda uma hierarquia de objetos desta forma, mas isso requer atravessar a hierarquia, consultar os limites de cada malha, e combiná-los manualmente. Esta operação é aborrecida e ineficiente.

Uma maneira melhor é ligar `QueryLocalBoundsAsync` ou `QueryWorldBoundsAsync` entrar numa entidade. O cálculo é então descarregado para o servidor e devolvido com o mínimo de atraso.

```cs
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

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>Passos seguintes

* [Consultas espaciais](../overview/features/spatial-queries.md)
