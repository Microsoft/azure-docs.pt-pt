---
title: 'Quickstart: Criar uma aplicação HoloLens com DirectX'
description: Neste arranque rápido, aprende-se a construir uma aplicação HoloLens utilizando Âncoras Espaciais.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 582fd38e54d08bdc8e1d892c569df9031dc8dc7a
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939635"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Quickstart: Criar uma aplicação HoloLens com âncoras espaciais Azure, em C++/WinRT e DirectX

Este quickstart cobre como criar uma aplicação HoloLens usando [âncoras espaciais Azure](../overview.md) em C++/WinRT e DirectX. Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação HoloLens que pode guardar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:
- Uma máquina Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> instalada com a carga **de trabalho de desenvolvimento** da Plataforma Universal Windows e o componente **Windows 10 SDK (10.0.18362.0 ou mais recente).** Também tem de instalar <a href="https://git-scm.com/download/win" target="_blank">o Git para Windows</a> e Git <a href="https://git-lfs.github.com/">LFS</a>.
- A [extensão do estúdio visual C++/WinRT (VSIX)](https://aka.ms/cppwinrt/vsix) para Visual Studio deve ser instalada a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Um dispositivo HoloLens com [modo de desenvolvimento](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativado. Este artigo requer um dispositivo HoloLens com a [atualização do Windows 10 May 2020](https://docs.microsoft.com/windows/mixed-reality/whats-new/release-notes-may-2020 ). Para atualizar a versão mais recente do HoloLens, abra a aplicação **Definições,** vá a **Update & Security**, selecione o botão Verificar para **atualizações.**
- A sua aplicação deve definir a capacidade **de perceção espacial** no seu manifesto AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Aberto `HoloLens\DirectX\SampleHoloLens.sln` no Estúdio Visual.

## <a name="configure-account-identifier-and-key"></a>Configure identificador de conta e chave

O próximo passo é configurar a app para usar o identificador de conta e a chave da conta. Copiou-os para um editor de texto ao [configurar o recurso Spatial Anchors](#create-a-spatial-anchors-resource).

Abra `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave de conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize o `SpatialAnchorsAccountDomain` campo e `Set me` substitua-o pelo domínio da conta.

## <a name="deploy-the-app-to-your-hololens"></a>Implemente a aplicação para os seus HoloLens

Alterar a Configuração de **Solução** para **Soltar,** alterar **a Plataforma de Solução** para **x86**e selecionar **o Dispositivo** a partir das opções-alvo de implementação.

Se utilizar hololens 2, utilize **o ARM64** como **Plataforma de Solução,** em vez de **x86**.

![Configuração visual do estúdio](./media/get-started-hololens/visual-studio-configuration.png)

Ligue o dispositivo HoloLens, inscreva-se e conecte-o ao PC utilizando um cabo USB.

Selecione **Debug**  >  **Comece a depurar** para implementar a sua aplicação e comece a depurar.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

No Visual Studio, pare a aplicação selecionando **Stop Debugging** ou pressionando **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
