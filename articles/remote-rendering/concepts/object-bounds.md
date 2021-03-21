---
title: Limites dos objetos
description: Explica como os limites dos objetos espaciais podem ser questionados
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594489"
---
# <a name="object-bounds"></a>Limites dos objetos

Os limites dos objetos representam o volume que uma [entidade](entities.md) e os seus filhos ocupam. Na renderização remota de Azure, os limites dos objetos são sempre dados como *caixas de delimitação alinhadas com o eixo* (AABB). Os limites dos objetos podem ser no *espaço local* ou no *espaço mundial.* De qualquer forma, estão sempre alinhados com eixos, o que significa que as extensões e o volume podem diferir entre a representação espacial local e mundial.

## <a name="querying-object-bounds"></a>Consulta limites de objeto

O eixo local alinhado na caixa de delimitação de uma [malha](meshes.md) pode ser consultado diretamente a partir do recurso de malha. Estes limites podem ser transformados no espaço local ou no espaço mundial de uma entidade usando a transformação da entidade.

É possível calcular os limites de toda uma hierarquia de objetos desta forma, mas isso requer atravessar a hierarquia, consultar os limites de cada malha, e combiná-los manualmente. Esta operação é aborrecida e ineficiente.

Uma maneira melhor é ligar `QueryLocalBoundsAsync` ou `QueryWorldBoundsAsync` entrar numa entidade. O cálculo é então descarregado para o servidor e devolvido com o mínimo de atraso.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# Entidade.QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entidade C++::QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Passos seguintes

* [Consultas espaciais](../overview/features/spatial-queries.md)