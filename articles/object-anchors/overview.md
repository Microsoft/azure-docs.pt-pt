---
title: Visão geral das âncoras de objetos Azure
description: Saiba como as âncoras de objetos Azure ajudam a detetar objetos no mundo físico.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: b4284411fbe09a0d0ce8412e6e5a5df464f35564
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462869"
---
# <a name="azure-object-anchors-overview"></a>Visão geral das âncoras de objetos Azure

A Azure Object Anchors permite que uma aplicação detete um objeto no mundo físico usando um modelo 3D e estime a sua pose 6DoF. A pose de 6DoF (6 graus de liberdade) é definida como uma rotação e tradução entre um modelo 3D e a sua contraparte física, o objeto real.

A Azure Object Anchors é composto por um serviço de conversão de modelos e um SDK de cliente de tempo de execução para HoloLens. O serviço aceita um modelo de objeto 3D e produz um modelo Azure Object Anchors. O modelo Azure Object Anchors é utilizado juntamente com o tempo de execução SDK para permitir que uma aplicação HoloLens carregue um modelo de objeto, detete e rastree a instância(s) desse modelo no mundo físico.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Âncoras de objeto azure em ação":::

## <a name="examples"></a>Exemplos

Alguns casos de utilização de exemplos ativados por Âncoras de Objetos Azure incluem:

- **Preparação**: Crie experiências de formação de Realidade Mista para os seus trabalhadores, sem necessidade de colocar marcadores ou passar tempo a ajustar manualmente o alinhamento do holograma. Se quiser aumentar as suas experiências de treino de Realidade Mista com deteção e rastreio automatizados, ingere o seu modelo no serviço Azure Object Anchors e estará um passo mais perto de uma experiência sem marcação.

- **Orientação de Tarefas**. Caminhar os colaboradores através de um conjunto de tarefas pode ser muito simplificado ao usar a Realidade Mista. Sobrepor instruções digitais e boas práticas, em cima do objeto físico – seja uma peça de maquinaria no chão de uma fábrica, ou uma cafeteira na cozinha da equipa – pode reduzir consideravelmente a dificuldade de completar o conjunto de tarefas. Desencadear estas experiências normalmente requer alguma forma de alinhamento marcador ou manual, mas com âncoras de objetos Azure, pode criar uma experiência que detete automaticamente o objeto relacionado com a tarefa em questão. Em seguida, fluir perfeitamente através da orientação da Realidade Mista sem marcadores ou alinhamento manual.

- **Procura de ativos**. Se já tiver um modelo 3D de algum objeto no seu espaço físico, as Âncoras de Objetos Azure podem permitir-lhe localizar e rastrear instâncias desse objeto no seu ambiente físico.

## <a name="next-steps"></a>Passos seguintes

As secções seguintes fornecem informações sobre como começar a usar e construir aplicativos com âncoras de objetos Azure.

> [!div class="nextstepaction"]
> [Ingestão de Modelos](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [HoloLens de unidade com MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
