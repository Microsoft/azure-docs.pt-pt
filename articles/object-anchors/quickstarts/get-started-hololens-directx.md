---
title: 'Quickstart: Criar uma aplicação HoloLens com DirectX'
description: Neste arranque rápido, aprende-se a construir uma aplicação HoloLens utilizando Âncoras de Objetos.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 8a02bb7c70df4fed55c354638fe6662b85e6c164
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049669"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Quickstart: Criar uma aplicação HoloLens com âncoras de objetos Azure, em C++/WinRT e DirectX

Este quickstart cobre como criar uma aplicação HoloLens usando [âncoras de objeto azure](../overview.md) em C++/WinRT e DirectX. Azure Object Anchors é um serviço de nuvem gerido que converte ativos 3D em modelos de IA que permitem experiências de realidade mista conscientes de objetos para os HoloLens. Quando terminar, terá uma aplicação HoloLens que pode detetar um objeto e a sua pose numa aplicação Holográfica DirectX 11 (Universal Windows).

Vai aprender a:

> [!div class="checklist"]
> * Criar e carregar lateralmente uma aplicação HoloLens
> * Detetar um objeto e visualizar o seu modelo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

* Um objeto físico no seu ambiente e no seu modelo 3D (CAD ou digitalizado).
* Uma máquina Windows com o seguinte instalado:
  * <a href="https://git-scm.com" target="_blank">Git para Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a carga de **trabalho de desenvolvimento da Plataforma Universal Windows** e o componente Windows **10 SDK (10.0.18362.0 ou mais recente)**
* Um dispositivo HoloLens 2 que está atualizado e tem [o modo de desenvolvimento](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio#enabling-developer-mode) ativado.
  * Para atualizar a versão mais recente do HoloLens, abra a aplicação **Definições,** vá a **Update & Security** e, em seguida, selecione Check for **updates**.

## <a name="open-the-sample-project"></a>Abra o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Aberto `quickstarts/apps/directx/DirectXAoaSampleApp.sln` no Estúdio Visual.

Alterar a Configuração da **Solução** para **Soltar,** alterar **a Plataforma de Solução** para **ARM64**, selecione **Dispositivo** a partir das opções-alvo de implementação. Em seguida, construa o projeto **AoaSampleApp** clicando no projeto e selecionando **Build.**

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Configure projeto visual studio para implementar":::

## <a name="deploy-the-app-to-hololens"></a>Implementar a aplicação para HoloLens

Depois de compilar o projeto da amostra com sucesso, pode implementar a aplicação para HoloLens.

Ligue o dispositivo HoloLens, inscreva-se e conecte-o ao PC utilizando um cabo USB. Certifique-se de que o **dispositivo** é o alvo de implantação escolhido (ver acima).

Clique com o botão direito do projeto **AoaSampleApp** e, em seguida, clique em **Implementar** a partir do menu pop-up para instalar a aplicação. Se não houver erro na **Janela** de Saída do Visual Studio, a aplicação será instalada no HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Implementar aplicativo para HoloLens":::

Antes de lançar a aplicação, tem de carregar um modelo de objeto. Siga as instruções no **modelo do objeto Ingeste e detete** a secção de exemplo abaixo.

Para lançar e depurar a aplicação, selecione **Debug > Comece a depurar**. Para parar a aplicação, selecione **Stop Debugging** ou prima **Shift + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Ingerir o modelo de objeto e detetar a sua instância

Terá de criar um modelo de objeto para executar a aplicação da amostra. Assuma que já tem um CAD ou um modelo de malha 3D digitalizado de um objeto no seu espaço. Consulte o [Quickstart: Ingerir um Modelo 3D](./get-started-model-conversion.md) sobre como criar um modelo.

Baixe esse modelo, **cadeira.ou** no nosso caso, para o seu computador. Em seguida, a partir do portal do dispositivo HoloLens, selecione **System > File explorer > LocalAppData > AoaSampleApp > LocalState** e selecione **Browse...**. Em seguida, selecione o seu ficheiro modelo, **chair.ou,** por exemplo, e selecione **Upload**. Em seguida, deve ver o ficheiro modelo na cache local.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Modelo de upload do portal":::

A partir dos HoloLens, lance a app **AoaSampleApp** (se já estava aberta, feche-a e reabra-a). Caminhe perto (a uma distância de 2 metros) até ao objeto alvo (cadeira) e digitalize-o olhando-o de várias perspetivas. Deve ver uma caixa de delimitação cor-de-rosa à volta do objeto com alguns pontos amarelos renderizados perto da superfície do objeto, o que indica que foi detetado.

:::image type="content" source="./media/chair-detection.png" alt-text="Deteção de cadeiras":::

Figura: uma cadeira detetada renderizada com a sua caixa de delimitação (rosa), nuvem de ponto (amarelo) e uma área de busca (caixa amarela grande).

Pode definir um espaço de pesquisa para o objeto na aplicação clicando no ar com a mão direita ou esquerda. O espaço de busca irá alternar entre uma esfera de raio de 2 metros, uma caixa de limites de 4 m^3 e um frustum de vista. Para objetos maiores como carros, a melhor escolha será normalmente usar a seleção de frustum vista enquanto está de frente para um canto do objeto a cerca de 2 metros de distância.
Cada vez que a área de pesquisa muda, a aplicação removerá as instâncias que estão a ser rastreadas e, em seguida, tentará encontrá-las novamente na nova área de pesquisa.

Esta aplicação pode rastrear vários objetos de uma só vez. Para isso, faça o upload de vários modelos para a pasta **Estado Local** e deslomeie uma área de pesquisa que cubra todos os objetos-alvo. Pode demorar mais tempo a detetar e rastrear vários objetos.

A aplicação alinha de perto um modelo 3D à sua contraparte física. Um utilizador pode tocar ar usando a mão esquerda para ligar o modo de rastreio de alta precisão, que calcula uma pose mais precisa. Esta é ainda uma característica experimental, que consome mais recursos do sistema, e pode resultar em maior nervosismo na pose estimada. Toque de ar novamente com a mão esquerda para voltar ao modo de localização normal.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Ingerir um Modelo 3D](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Conceitos: Visão geral do SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)
