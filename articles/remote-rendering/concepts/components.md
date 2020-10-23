---
title: Componentes
description: Definição de componentes no âmbito da renderização remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: a17bfe4dac2007d3ad136598c3c4e335e2397293
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203726"
---
# <a name="components"></a>Componentes

A renderização remota Azure utiliza o [padrão do Sistema de Componentes da Entidade.](https://en.wikipedia.org/wiki/Entity_component_system) Enquanto [as entidades](entities.md) representam a posição e a composição hierárquica dos objetos, os componentes são responsáveis pela implementação do comportamento.

Os tipos de componentes mais utilizados [:::no-loc text="mesh components":::](meshes.md) são, que adicionam malhas no gasoduto de renderização. Da mesma forma, os componentes de luz são [usados](../overview/features/lights.md) para adicionar iluminação e [componentes de plano cortados](../overview/features/cut-planes.md) são usados para cortar malhas abertas.

Todos estes componentes utilizam a transformação (posição, rotação, escala) da entidade a que estão ligados, como ponto de referência.

## <a name="working-with-components"></a>Trabalhar com componentes

Pode facilmente adicionar, remover e manipular componentes programáticamente:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

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
* [C# RemoteManager.CreateComponent()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.createcomponent)
* [C# Entidade.FindComponentOfType()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [Base de Componentes C++](/cpp/api/remote-rendering/componentbase)
* [C++ RemoteManager::CreateComponent()](/cpp/api/remote-rendering/remotemanager#createcomponent)
* [C++ Entidade::FindComponentOfType()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Passos seguintes

* [Limites dos objetos](object-bounds.md)
* [Malhas](meshes.md)