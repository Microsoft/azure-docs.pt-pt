---
title: Componentes
description: Definição de componentes no âmbito da renderização remota azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681899"
---
# <a name="components"></a>Componentes

A renderização remota Azure utiliza o padrão do Sistema de Componentes da [Entidade.](https://en.wikipedia.org/wiki/Entity_component_system) Enquanto [as entidades](entities.md) representam a posição e a composição hierárquica dos objetos, os componentes são responsáveis pela implementação do comportamento.

Os tipos de componentes mais utilizados são os componentes da [malha,](meshes.md)que adicionam malhas no gasoduto de renderização. Da mesma forma, os componentes de luz são [usados](../overview/features/lights.md) para adicionar iluminação e [cortar componentes do plano](../overview/features/cut-planes.md) são usados para cortar malshes abertos.

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

Um componente é ligado a uma entidade no momento da criação. Não pode ser transferido para outra entidade depois. Os componentes são explicitamente eliminados com `Component.Destroy()` ou automaticamente quando a entidade proprietária do componente é destruída.

Apenas uma instância de cada tipo de componente pode ser adicionada a uma entidade de cada vez.

## <a name="unity-specific"></a>Unidade específica

A integração da Unidade tem funções adicionais de extensão para interagir com componentes. Ver [Objetos e componentes do jogo da Unidade](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Passos seguintes

* [Limites de objetos](object-bounds.md)
* [Meshes](meshes.md)
