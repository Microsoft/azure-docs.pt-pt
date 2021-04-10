---
title: Consultas espaciais
description: Como fazer consultas espaciais numa cena
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c664df586c260b3e16f64c071190055dbaeccd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594049"
---
# <a name="spatial-queries"></a>Consultas espaciais

Consultas espaciais são operações com as quais pode perguntar ao serviço de renderização remota quais os objetos localizados numa área. As consultas espaciais são frequentemente usadas para implementar interações, tais como descobrir qual o objeto que um utilizador está a apontar.

Todas as consultas espaciais são avaliadas no servidor. Consequentemente, são operações assíncronos e os resultados chegarão com um atraso que depende da latência da sua rede. Uma vez que cada consulta espacial gera tráfego de rede, tenha cuidado para não fazer muito ao mesmo tempo.

## <a name="collision-meshes"></a>Malhas de colisão

As consultas espaciais são alimentadas pelo motor [Havok Physics](https://www.havok.com/products/havok-physics) e requerem uma malha de colisão dedicada para estar presente. Por predefinição, [a conversão do modelo](../../how-tos/conversion/model-conversion.md) gera malhas de colisão. Se não necessitar de consultas espaciais num modelo complexo, considere a geração de malha de colisão incapacitante nas opções de [conversão,](../../how-tos/conversion/configure-model-conversion.md)uma vez que tem um impacto de várias formas:

* [A conversão do modelo](../../how-tos/conversion/model-conversion.md) demorará consideravelmente mais tempo.
* Os tamanhos de ficheiros de modelos convertidos são visivelmente maiores, impactando a velocidade de descarregamento.
* Os tempos de execução são mais longos.
* O consumo de memória do CPU em tempo de execução é maior.
* Há um ligeiro desempenho de tempo de execução para cada exemplo.

## <a name="ray-casts"></a>Moldes de raios

Um *elenco de raios* é uma consulta espacial onde o tempo de execução verifica quais os objetos que são intersectados por um raio, começando numa determinada posição e apontando para uma determinada direção. Como otimização, uma distância máxima de raios também é dada, para não procurar objetos que estão muito longe.

```cs
async void CastRay(RenderingSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastQueryResult result = await session.Connection.RayCastQueryAsync(rayCast);
    RayCastHit[] hits = result.Hits;
    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<RenderingSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = {0, 0, 0};
    rayCast.EndPos = {0, 0, 1};
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    session->Connection()->RayCastQueryAsync(rayCast, [](Status status, ApiHandle<RayCastQueryResult> result)
    {
        if (status == Status::OK)
        {
            std::vector<RayCastHit> hits;
            result->GetHits(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        }
    });
}
```


Existem três modos de recolha de sucesso:

* **`Closest` :** Neste modo, apenas o impacto mais próximo será reportado.
* **`Any` :** Prefira este modo quando tudo o que quer saber é *se* um raio atingiria alguma coisa, mas não se importa com o que foi atingido exatamente. Esta consulta pode ser consideravelmente mais barata para avaliar, mas também tem poucas aplicações.
* **`All` :** Neste modo, todos os acessos ao longo do raio são relatados, ordenados à distância. Não use este modo a menos que precise de mais do que o primeiro golpe. Limite o número de acessos reportados com a `MaxHits` opção.

Para excluir os objetos seletivamente de serem considerados para moldes de raios, o componente [HierarchicalStateOverrideComponent](override-hierarchical-state.md) pode ser utilizado.

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Resultado do sucesso

O resultado de uma consulta de ray cast é uma série de acessos. A matriz está vazia, se nenhum objeto foi atingido.

A Hit tem as seguintes propriedades:

* **`HitEntity` :** Que [entidade](../../concepts/entities.md) foi atingida.
* **`SubPartId` :** Que *submesh* foi atingido num [MeshComponent](../../concepts/meshes.md). Pode ser usado para indexar `MeshComponent.UsedMaterials` e procurar o [material](../../concepts/materials.md) naquele ponto.
* **`HitPosition` :** A posição espacial do mundo onde o raio cruzou o objeto.
* **`HitNormal` :** A superfície espacial mundial normal da malha na posição do cruzamento.
* **`DistanceToHit` :** A distância entre a posição inicial do raio e o impacto.

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection.RayCastQueryAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.raycastqueryasync)
* [C++ RenderingConnection::RayCastQueryAsync()](/cpp/api/remote-rendering/renderingconnection#raycastqueryasync)

## <a name="next-steps"></a>Passos seguintes

* [Limites dos objetos](../../concepts/object-bounds.md)
* [Estados hierárquicos dominantes](override-hierarchical-state.md)