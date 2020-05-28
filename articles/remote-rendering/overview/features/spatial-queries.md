---
title: Consultas espaciais
description: Como fazer consultas espaciais numa cena
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 3f808d45197f7d9ee23d3f809a2ab0452e92c20e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021301"
---
# <a name="spatial-queries"></a>Consultas espaciais

Consultas espaciais são operações com as quais pode perguntar ao serviço de renderização remota quais os objetos que estão localizados numa área. Consultas espaciais são frequentemente usadas para implementar interações, tais como descobrir qual o objeto que um utilizador está apontando.

Todas as consultas espaciais são avaliadas no servidor. Consequentemente, são operações assíncronas e os resultados chegarão com um atraso que depende da latência da sua rede. Uma vez que todas as consultas espaciais geram tráfego de rede, tenha cuidado para não fazer muitas ao mesmo tempo.

## <a name="collision-meshes"></a>Meshes de colisão

As consultas espaciais são alimentadas pelo motor de [Física havok](https://www.havok.com/products/havok-physics) e requerem uma malha de colisão dedicada para estar presente. Por padrão, a conversão do [modelo](../../how-tos/conversion/model-conversion.md) gera maquetes de colisão. Se não necessitar de consultas espaciais num modelo complexo, considere desativar a geração de malha de colisão nas opções de [conversão,](../../how-tos/conversion/configure-model-conversion.md)uma vez que tem um impacto de várias maneiras:

* [A conversão](../../how-tos/conversion/model-conversion.md) do modelo demorará consideravelmente mais tempo.
* Os tamanhos de ficheiros de modelos convertidos são visivelmente maiores, com impacto na velocidade de descarregamento.
* Os tempos de tempo de execução são mais longos.
* O consumo de memória cpu de tempo de execução é maior.
* Há um ligeiro desempenho em execução para cada modelo.

## <a name="ray-casts"></a>Moldes de ray

Um *molde de raios* é uma consulta espacial onde o tempo de execução verifica quais os objetos que são interseccionados por um raio, começando numa determinada posição e apontando para uma determinada direção. Como otimização, também é dada uma distância máxima de raios, para não procurar objetos que estão muito longe.

```cs
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

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
void CastRay(ApiHandle<AzureSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = { 0, 0, 0 };
    rayCast.EndPos = { 0, 0, 1 };
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    ApiHandle<RaycastQueryAsync> castQuery = *session->Actions()->RayCastQueryAsync(rayCast);

    castQuery->Completed([](const ApiHandle<RaycastQueryAsync>& async)
    {
        std::vector<RayCastHit> hits = *async->Result();

        if (hits.size() > 0)
        {
            auto hitObject = hits[0].HitObject;
            auto hitPosition = hits[0].HitPosition;
            auto hitNormal = hits[0].HitNormal;

            // do something with the hit information
        }
    });

}
```


Existem três modos de recolha de sucesso:

* ** `Closest` :** Neste modo, apenas será reportado o impacto mais próximo.
* ** `Any` :** Prefira este modo quando tudo o que quer saber é *se* um raio atingiria alguma coisa, mas não se importa exatamente com o que foi atingido. Esta consulta pode ser consideravelmente mais barata para avaliar, mas também tem poucas aplicações.
* ** `All` :** Neste modo, todos os acessos ao longo do raio são relatados, classificados à distância. Não use este modo a menos que precise de mais do que o primeiro sucesso. Limite o número de acessos reportados com a `MaxHits` opção.

Para excluir objetos seletivamente de serem considerados para moldes de raios, o componente [HierarchicalStateOverrideComponent](override-hierarchical-state.md) pode ser utilizado.

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Resultado de sucesso

O resultado de uma consulta de ray cast é uma série de sucessos. A matriz está vazia, se nenhum objeto foi atingido.

A Hit tem as seguintes propriedades:

* ** `HitEntity` :** Que [entidade](../../concepts/entities.md) foi atingida.
* ** `SubPartId` :** Qual *submalha* foi atingida num [MeshComponent](../../concepts/meshes.md). Pode ser usado para indexar `MeshComponent.UsedMaterials` e procurar o [material](../../concepts/materials.md) naquele ponto.
* ** `HitPosition` :** A posição espacial mundial onde o raio cruzou o objeto.
* ** `HitNormal` :** A superfície espacial mundial normal da malha na posição do cruzamento.
* ** `DistanceToHit` :** A distância da posição de arranque do raio até ao golpe.

## <a name="next-steps"></a>Próximos passos

* [Limites dos objetos](../../concepts/object-bounds.md)
* [Estados hierárquicos dominantes](override-hierarchical-state.md)
