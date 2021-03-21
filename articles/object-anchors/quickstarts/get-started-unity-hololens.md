---
title: 'Quickstart: Criar uma app HoloLens com Unidade'
description: Neste arranque rápido, aprende-se a construir uma aplicação HoloLens Unitity utilizando Âncoras de Objetos.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 4f85a258042430d58690ef578db6d21a6c831d50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044808"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Quickstart: Criar uma app HoloLens com âncoras de objetos Azure, em Unidade

Neste arranque rápido, você vai criar uma aplicação Unity HoloLens que usa [Âncoras de Objeto Azure](../overview.md). Azure Object Anchors é um serviço de nuvem gerido que converte ativos 3D em modelos de IA que permitem experiências de realidade mista conscientes de objetos para os HoloLens. Quando terminar, terá uma aplicação HoloLens construída com Unidade que pode detetar objetos no mundo físico.

Vai aprender a:

> [!div class="checklist"]
> * Prepare as definições de construção da Unidade.
> * Exportar o projeto HoloLens Visual Studio.
> * Implemente a aplicação e execute-a num dispositivo HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

In Unitity, abra o `quickstarts/apps/unity/basic` projeto.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Ligue o dispositivo, selecione **Todas as Aplicações** e, em seguida, localize e inicie a aplicação. Depois do ecrã de respingo Da Unidade, verá uma mensagem indicando que o Observador de Objetos foi inicializado. No entanto, terá de adicionar o seu modelo à aplicação.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

A aplicação procura objetos no campo de visão atual e, em seguida, rastreia-os uma vez detetados. Um caso será removido quando estiver a 6 metros da localização do utilizador. O texto de depuração mostra detalhes sobre um caso, como iD, temperatura de tempo atualizada e rácio de cobertura de superfície.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [HoloLens de unidade com MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Conceitos: Visão geral do SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)
