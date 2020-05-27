---
title: 'Quickstart: Criar uma aplicação HoloLens com DirectX'
description: Neste arranque rápido, aprende-se a construir uma aplicação HoloLens utilizando âncoras espaciais.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 582bb3e0f98535ccdc7be059de904e152db6ac77
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870993"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Quickstart: Criar uma aplicação HoloLens com Âncoras Espaciais Azure, em C++/WinRT e DirectX

Este quickstart cobre como criar uma aplicação HoloLens utilizando [âncoras espaciais Azure](../overview.md) em C++/WinRT e DirectX. O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação HoloLens que pode salvar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:
- Uma máquina Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> instalada com a carga de trabalho de desenvolvimento da **Plataforma Universal Windows** e o componente Windows **10 SDK (10.0.18362.0 ou mais recente).** Também deve instalar <a href="https://git-scm.com/download/win" target="_blank">git para Windows</a> e <a href="https://git-lfs.github.com/">Git LFS</a>.
- A [extensão do estúdio visual C++/WinRT (VSIX)](https://aka.ms/cppwinrt/vsix) para Estúdio Visual deve ser instalada a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Um dispositivo HoloLens com [modo de desenvolvimento](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativado. Este artigo requer um dispositivo HoloLens com a [Atualização do Windows 10 de outubro de 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (também conhecida como RS5). Para atualizar a mais recente versão do HoloLens, abra a aplicação **Definições,** vá ao **Update & Security**e, em seguida, selecione o botão **'Verificar' para atualizações.**
- A sua aplicação deve definir a capacidade **espacialPerception** no seu manifesto AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto da amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Aberto `HoloLens\DirectX\SampleHoloLens.sln` no Estúdio Visual.

## <a name="configure-account-identifier-and-key"></a>Configurar identificador de conta e chave

O próximo passo é configurar a aplicação para utilizar o identificador de conta e a chave de conta. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

Abra `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave da conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize `case DemoStep::ConfigSession:` e adicione a seguinte linha, substituindo no domínio da sua conta anterior: `configuration.AccountDomain("MyAccountDomain");` .

## <a name="deploy-the-app-to-your-hololens"></a>Implemente a aplicação para os seus HoloLens

Altere a configuração da **solução** para **o lançamento,** mude a **Plataforma de Soluções** para **x86**e selecione **o Dispositivo** a partir das opções-alvo de implementação.

Se utilizar hololens 2, utilize o **ARM64** como Plataforma de **Solução**, em vez de **x86**.

![Configuração do estúdio visual](./media/get-started-hololens/visual-studio-configuration.png)

Ligue o dispositivo HoloLens, inscreva-se e ligue-o ao PC utilizando um cabo USB.

Selecione **Debug**  >  **Comece a depurar-se** para implementar a sua aplicação e comece a depurar.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

No Estúdio Visual, pare a aplicação selecionando **Stop Debugging** ou pressionando **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
