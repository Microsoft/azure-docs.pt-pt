---
title: Carregar imagens para o aplicativo de IoT Central do Azure | Microsoft Docs
description: Como um construtor, saiba como preparar e carregar imagens para seu aplicativo de IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d8a863ee971fa154740f3726034fc4604b02fd34
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949927"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparar e carregar imagens para seu aplicativo de IoT Central do Azure

Este artigo descreve como, como um construtor, você pode personalizar seu aplicativo de IoT Central do Azure carregando imagens personalizadas. Por exemplo, você pode personalizar um painel de dispositivos com uma imagem do dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

1. Um aplicativo IoT Central do Azure. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
1. Uma ferramenta para dimensionar e redimensionar arquivos de imagens.

## <a name="choose-where-to-use-custom-images"></a>Escolher onde usar imagens personalizadas

Você pode adicionar imagens personalizadas aos seguintes locais em um aplicativo de IoT Central do Azure:

* A página **meus aplicativos**

    ![Imagem na página do Gerenciador de aplicativos](media/howto-prepare-images/applicationmanager.png)

* O painel do aplicativo

    ![Imagem no painel do aplicativo](media/howto-prepare-images/homepage.png)

* Um modelo de dispositivo

    ![Imagem no modelo de dispositivo](media/howto-prepare-images/devicetemplate.png)

* Um bloco em um painel do dispositivo

    ![Imagem no bloco do dispositivo](media/howto-prepare-images/devicetile.png)

* Um bloco em um painel de conjunto de dispositivos

    ![Imagem no bloco de conjunto de dispositivos](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Preparar as imagens

Em todos os quatro locais, você pode usar as imagens PNG, GIF ou JPEG.

A tabela a seguir resume os tamanhos de imagem que você pode usar:

| Localização | Tamanhos |
| -------- | ------ |
| Gerenciador de aplicativos | 268x160 px |
| Modelo de dispositivo | 64 x 64 px |
| Blocos de painel | O bloco de tamanho menor é 200 x 200 px, blocos maiores podem ser múltiplos quadrados ou retangulares de blocos pequenos. Por exemplo, 200x400 px, 400x200 PX ou 400x400 px |

Para obter a melhor exibição no aplicativo, você deve criar imagens que correspondam às dimensões mostradas na tabela anterior.

## <a name="upload-the-images"></a>Carregar as imagens

As seções a seguir descrevem como carregar as imagens em locais diferentes:

### <a name="application-manager"></a>Gerenciador de aplicativos

Para carregar uma imagem a ser usada na página **meus aplicativos** , navegue até a página **configurações do aplicativo** na seção **Administração** . Você deve ser um administrador para concluir esta tarefa:

![Carregar imagem do aplicativo](media/howto-prepare-images/uploadapplicationmanager.png)

Selecione o bloco **imagem do aplicativo** para carregar uma imagem (268x160 px) do computador local.

### <a name="application-dashboard"></a>Dashboard de aplicações

Para carregar uma imagem no painel do aplicativo, navegue até a página **painel** do seu aplicativo e selecione **Editar**. Você deve ser um construtor para concluir esta tarefa:

![Carregar imagem do painel](media/howto-prepare-images/uploadhomepage.png)

Em **Configurar imagem**, selecione o bloco **imagem** para carregar uma imagem do computador local. O bloco de tamanho menor é 200 x 200 px, blocos maiores podem ser múltiplos quadrados ou retangulares de blocos pequenos. Por exemplo, 200x400 px, 400x200 PX ou 400x400 px.

**Salve** a imagem carregada. Você pode redimensioná-lo enquanto estiver no modo de edição. Selecione **concluído** quando terminar.

### <a name="device-template"></a>Modelo de dispositivo

Para carregar uma imagem em um modelo de dispositivo, navegue até **modelos de dispositivo** e escolha o modelo de dispositivo. Você deve ser um construtor para concluir esta tarefa:

![Carregar imagem de modelo de dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Selecione o bloco imagem para carregar uma imagem (64 x 64 px) do computador local.

### <a name="device-dashboard"></a>Dashboard do dispositivo

Para carregar uma imagem em um painel do dispositivo, navegue até **modelos de dispositivo** e escolha o modelo de dispositivo. Em seguida, escolha a guia **painel** . Você deve ser um construtor para concluir esta tarefa:

![Carregar imagem do painel do dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

Em **Configurar imagem**, selecione o bloco **imagem** e, em seguida, escolha o arquivo a ser carregado em seu computador local. O bloco de tamanho menor é 200 x 200 px, blocos maiores podem ser múltiplos quadrados ou retangulares de blocos pequenos. Por exemplo, 200x400 px, 400x200 PX ou 400x400 px.

**Salve** a imagem carregada. Você pode redimensioná-lo e reposicioná-lo enquanto estiver no modo de edição. Selecione **concluído** quando terminar.

### <a name="device-set-dashboard"></a>Painel do conjunto de dispositivos

Para carregar uma imagem em um painel de conjunto de dispositivos, navegue até **conjuntos de dispositivos** e escolha o conjunto de dispositivos e, em seguida, um dispositivo. Em seguida, escolha a página **painel** e selecione **Editar**:

![Carregar imagem do painel do conjunto de dispositivos](media/howto-prepare-images/uploaddevicesetdashboard.png)

Em **Configurar imagem**, selecione o bloco **imagem** para carregar uma imagem do computador local. O bloco de tamanho menor é 200 x 200 px, blocos maiores podem ser múltiplos quadrados ou retangulares de blocos pequenos. Por exemplo, 200x400 px, 400x200 PX ou 400x400 px.

**Salve** a imagem carregada. Você pode redimensioná-lo e reposicioná-lo enquanto estiver no modo de edição. Selecione **concluído** quando terminar.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como preparar e carregar imagens para seu aplicativo IoT Central do Azure, aqui estão as próximas etapas sugeridas:

* [Personalizar a interface do usuário do IoT Central do Azure](./howto-customize-ui.md)
* [Adicionar blocos ao seu painel](./howto-add-tiles-to-your-dashboard.md)
* [Gerenciar dispositivos em seu aplicativo de IoT Central do Azure](howto-manage-devices.md)
