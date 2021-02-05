---
title: Componentes
description: Definição de componentes no âmbito da renderização remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592199"
---
# <a name="components"></a>Componentes

A renderização remota Azure utiliza o [padrão do Sistema de Componentes da Entidade.](https://en.wikipedia.org/wiki/Entity_component_system) Enquanto [as entidades](entities.md) representam a posição e a composição hierárquica dos objetos, os componentes são responsáveis pela implementação do comportamento.

Os tipos de componentes mais utilizados [:::no-loc text="mesh components":::](meshes.md) são, que adicionam malhas no gasoduto de renderização. Da mesma forma, os componentes de luz são [usados](../overview/features/lights.md) para adicionar iluminação e [componentes de plano cortados](../overview/features/cut-planes.md) são usados para cortar malhas abertas.

Todos estes componentes utilizam a transformação (posição, rotação, escala) da entidade a que estão ligados, como ponto de referência.

## <a name="working-with-components"></a>Trabalhar com componentes

Pode facilmente adicionar, remover e manipular componentes programáticamente:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Um componente é ligado a uma entidade no momento da criação. Não pode ser transferido para outra entidade depois. Os componentes são explicitamente eliminados com `Component.Destroy()` ou automaticamente quando a entidade proprietária do componente é destruída.

Apenas uma instância de cada tipo de componente pode ser adicionada a uma entidade de cada vez.

## <a name="unity-specific"></a>Unidade específica

A integração da Unidade tem funções de extensão adicionais para interagir com componentes. Consulte [objetos e componentes de jogos de unidade](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>Documentação da API

* [C# Base de Componentes](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection.CreateComponent()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C# Entidade.FindComponentOfType()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [Base de Componentes C++](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection::CreateComponent()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C++ Entidade::FindComponentOfType()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Passos seguintes

* [Limites dos objetos](object-bounds.md)
* [Malhas](meshes.md)