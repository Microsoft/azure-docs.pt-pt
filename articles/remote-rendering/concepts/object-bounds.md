---
title: Limites dos objetos
description: Explica como os limites dos objetos espaciais podem ser questionados
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: bd2b1026f69b5ec5814124a92d6a78d7f0225143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613817"
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

## <a name="api-documentation"></a>Documentação da API

* [C# Entidade.QueryLocalBoundsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entidade C++::QueryLocalBoundsAsync](https://docs.microsoft.com/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Passos seguintes

* [Consultas espaciais](../overview/features/spatial-queries.md)
