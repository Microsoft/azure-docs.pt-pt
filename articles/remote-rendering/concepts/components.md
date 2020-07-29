---
title: Componentes
description: Definição de componentes no âmbito da renderização remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: bd2a6e16d34bf6c5b59ce7fa5c99975d44947770
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021675"
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

## <a name="next-steps"></a>Próximos passos

* [Limites dos objetos](object-bounds.md)
* [Malhas](meshes.md)
