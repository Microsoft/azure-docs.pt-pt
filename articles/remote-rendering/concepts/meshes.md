---
title: Malhas
description: Definição de malhas no âmbito da renderização remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594523"
---
# <a name="meshes"></a>Malhas

## <a name="mesh-resource"></a>Recurso de malha

As malhas são um [recurso partilhado](../concepts/lifetime.md)imutável, que só pode ser criado através da conversão de [modelos.](../how-tos/conversion/model-conversion.md) As malhas contêm uma ou *múltiplas submechas* juntamente com uma representação física para [consultas de raycast](../overview/features/spatial-queries.md). Cada submesh refere um [material](materials.md) com o qual deve ser prestado por defeito. Para colocar uma malha no espaço 3D, adicione um [MeshComponent](#meshcomponent) a uma [Entidade.](entities.md)

### <a name="mesh-resource-properties"></a>Propriedades de recursos de malha

As `Mesh` propriedades da classe são:

* **Materiais:** Uma variedade de materiais. Cada material é utilizado por um submesh diferente. Várias entradas na matriz podem fazer referência ao mesmo [material](materials.md). Estes dados não podem ser modificados em tempo de execução.

* **Limites:** Uma caixa de delimitação alinhada com o eixo local (AABB) dos vértices da malha.

## <a name="meshcomponent"></a>MeshComponent

A `MeshComponent` classe é usada para colocar um exemplo de um recurso de malha. Cada MeshComponent refere uma única malha. Pode sobrepor-se aos materiais utilizados para render cada submesh.

### <a name="meshcomponent-properties"></a>Propriedades MeshComponent

* **Malha:** O recurso de malha que é utilizado por este componente.

* **Materiais:** A matriz de materiais especificados no próprio componente da malha. A matriz terá sempre o mesmo comprimento que a matriz *de Materiais* no recurso de malha. Os materiais que não devem ser ultrapassados do predefinido da malha, são definidos como *nulos* nesta matriz.

* **Materiais Usados:** A matriz de materiais realmente usados para cada submesh. Será idêntico aos dados da matriz *de Materiais,* para valores não nulos. Caso contrário, contém o valor da matriz *de materiais* na placa de malha.

### <a name="sharing-of-meshes"></a>Partilha de malhas

Um `Mesh` recurso pode ser partilhado em várias instâncias de componentes de malha. Além disso, o `Mesh` recurso atribuído a um componente de malha pode ser alterado programáticamente a qualquer momento. O código abaixo demonstra como clonar uma malha:

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>Documentação da API

* [Classe C# Malha](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# Classe MeshComponent](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Classe C++ malha](/cpp/api/remote-rendering/mesh)
* [Classe C++ MeshComponent](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Passos seguintes

* [Materiais](materials.md)