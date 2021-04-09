---
title: Entidades
description: Definição de entidades no âmbito da API de Renderização Remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 29952353b8c3452d95bcced163fafa81fe158f64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593406"
---
# <a name="entities"></a>Entidades

Uma *Entidade* representa um objeto móvel no espaço e é o bloco fundamental de construção de conteúdo prestado remotamente.

## <a name="entity-properties"></a>Propriedades de entidades

As entidades têm uma transformação definida por uma posição, rotação e escala. Por si só, as entidades não têm qualquer funcionalidade observável. Em vez disso, o comportamento é adicionado através de componentes, que estão ligados a entidades. Por exemplo, a anexação de um [CutPlaneComponent](../overview/features/cut-planes.md)  criará um plano de corte na posição da entidade.

O aspeto mais importante da própria entidade é a hierarquia e a transformação hierárquica resultante. Por exemplo, quando várias entidades são ligadas como crianças a uma entidade-mãe partilhada, todas estas entidades podem ser movidas, rotativas e dimensionadas em uníssono, alterando a transformação da entidade-mãe. Além disso, o estado da entidade `enabled` pode ser usado para desligar a visibilidade e as respostas aos moldes de raios para um gráfico completo na hierarquia.

Uma entidade é propriedade única do seu progenitor, o que significa que quando o progenitor é destruído `Entity.Destroy()` com, assim como os seus filhos e todos os [componentes conectados.](components.md) Assim, a remoção de um modelo da cena é conseguida invocando `Destroy` o nó raiz de um modelo, devolvido por ou a sua variante `RenderingSession.Connection.LoadModelAsync()` `RenderingSession.Connection.LoadModelFromSasAsync()` SAS.

As entidades são criadas quando o servidor carrega o conteúdo ou quando o utilizador quer adicionar um objeto à cena. Por exemplo, se um utilizador quiser adicionar um plano de corte para visualizar o interior de uma malha, o utilizador pode criar uma entidade onde o avião deve existir e, em seguida, adicionar-lhe o componente do plano de corte.

## <a name="create-an-entity"></a>Criar uma entidade

Para adicionar uma nova entidade à cena, por exemplo, para passá-la como um objeto de raiz para carregar modelos ou para anexar componentes ao mesmo, utilize o seguinte código:

```cs
Entity CreateNewEntity(RenderingSession session)
{
    Entity entity = session.Connection.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<RenderingSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Connection()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Funções de consulta

Existem dois tipos de funções de consulta em entidades: chamadas sincronizadas e assíncronos. Consultas sincronizadas só podem ser usadas para dados que estejam presentes no cliente e não envolvam muita computação. Exemplos são a consulta de componentes, transformações de objetos relativos ou relações entre pais e filhos. Consultas assíncronos são usadas para dados que residem apenas no servidor ou envolvem cálculo extra que seria demasiado caro para ser executado no cliente. Exemplos são consultas de limites espaciais ou consultas de meta dados.

### <a name="querying-components"></a>Componentes de consulta

Para encontrar um componente de um tipo específico, `FindComponentOfType` utilize:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Consulta transforma-se

As consultas de transformação são chamadas sincronizadas no objeto. É importante notar que as transformações consultadas através da API são transformações espaciais locais, em relação ao progenitor do objeto. Exceções são objetos de raiz, para os quais o espaço local e o espaço mundial são idênticos.

> [!NOTE]
> Não existe uma API dedicada para consultar a transformação do espaço mundial de objetos arbitrários.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>Consulta de limites espaciais

As consultas bounds são chamadas assíncronos que operam numa hierarquia de objetos completos, usando uma entidade como raiz. Consulte o capítulo dedicado sobre [os limites dos objetos.](object-bounds.md)

### <a name="querying-metadata"></a>Consulta de metadados

Metadados são dados adicionais armazenados em objetos, que são ignorados pelo servidor. Os metadados de objeto são essencialmente um conjunto de pares (nome, valor), onde _o valor_ pode ser de tipo numérico, booleano ou de corda. Os metadados podem ser exportados com o modelo.

As consultas de metadados são chamadas assíncronos numa entidade específica. A consulta apenas devolve os metadados de uma única entidade, e não a informação fundida de um sub-gráfico.

```cs
Task<ObjectMetadata> metaDataQuery = entity.QueryMetadataAsync();
ObjectMetadata metaData = await metaDataQuery;
ObjectMetadataEntry entry = metaData.GetMetadataByName("MyInt64Value");
System.Int64 intValue = entry.AsInt64;
// ...
```

```cpp
entity->QueryMetadataAsync([](Status status, ApiHandle<ObjectMetadata> metaData) 
{
    if (status == Status::OK)
    {
        ApiHandle<ObjectMetadataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
        int64_t intValue = *entry->GetAsInt64();

        // ...
    }
});
```

A consulta terá sucesso mesmo que o objeto não possua nenhum metadados.

## <a name="api-documentation"></a>Documentação da API

* [Classe de entidade](/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RenderingConnection.CreateEntity()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createentity)
* [Classe de entidade C++](/cpp/api/remote-rendering/entity)
* [C++ RenderingConnection::CreateEntity()](/cpp/api/remote-rendering/renderingconnection#createentity)

## <a name="next-steps"></a>Passos seguintes

* [Componentes](components.md)
* [Limites dos objetos](object-bounds.md)