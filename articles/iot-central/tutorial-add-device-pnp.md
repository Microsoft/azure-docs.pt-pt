---
title: Adicionar um dispositivo real a uma aplicação do Azure IoT Central | Microsoft Docs
description: Como operador, adicione um dispositivo real à aplicação do Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878174"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Tutorial: Adicionar um dispositivo simulado ao aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra como adicionar e configurar um dispositivo simulado ao seu aplicativo Microsoft Azure IoT Central.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar um novo dispositivo simulado
> * Usar um dispositivo simulado na experiência de compilação

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, o construtor deve concluir o primeiro tutorial do construtor para criar o aplicativo de IoT Central do Azure:

* [Definir um novo tipo de dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (obrigatório)

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Para adicionar um dispositivo real ao seu aplicativo, use o modelo de dispositivo do **sensor ambiental** criado no tutorial [definir um novo tipo de dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

1. Para adicionar um novo dispositivo como um operador, escolha **dispositivos** no menu de navegação à esquerda. A guia **dispositivos** mostra **todos os dispositivos** e o modelo de dispositivo do **sensor ambiental** .

1. Para adicionar um dispositivo de sensor de ambiente simulado, selecione **+ novo**. Use a **ID de dispositivo** sugerida ou insira sua própria **ID de dispositivo**em minúsculas. Você também pode inserir um nome para o novo dispositivo. Alterne a alternância simulada para **ativado** e, em seguida, selecione **criar**.

    ![Dispositivo simulado](./media/tutorial-add-device-pnp/simulated-device.png)

Agora você pode interagir com as exibições que foram criadas pelo construtor para o modelo de dispositivo usando dados simulados.

## <a name="use-a-simulated-device-to-improve-views"></a>Usar um dispositivo simulado para melhorar as exibições

Depois de criar um novo dispositivo simulado, o construtor pode usar este dispositivo para continuar a melhorar e criar as exibições para o modelo de dispositivo.

1. Enquanto estiver no modo de exibição do dispositivo, copie a **ID** do dispositivo simulado que você criou.

1. Escolha a guia **modelos de dispositivo** no menu de navegação à esquerda e selecione o modelo de **sensor ambiental** .

1. Selecione qualquer uma das exibições que você deseja editar ou crie uma nova exibição. Clique em **Configurar visualização do dispositivo**. Aqui você pode escolher entre não ter nenhum dispositivo de visualização, usando um dispositivo real que pode ser configurado para teste ou de um dispositivo existente que você adicionou ao IoT Central.

1. Escolha **selecionar em um dispositivo em execução** e insira a **ID** do dispositivo simulado que você copiou.

1. Escolha **Aplicar**. Agora você pode ver o mesmo dispositivo simulado em seus modos de exibição de modelo de dispositivo experiência de criação. Essa exibição é particularmente útil para gráficos e outras visualizações.

    ![Configurar o dispositivo de visualização](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

* Adicionar um novo dispositivo simulado
* Usar um dispositivo simulado na experiência de compilação

Agora que você conectou um dispositivo simulado ao aplicativo IoT Central do Azure, aqui estão algumas próximas etapas sugeridas.

Como operador, vai aprender a:

> [!div class="nextstepaction"]
> [Configurar regras](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

Como programador de dispositivos, vai aprender a:

> [!div class="nextstepaction"]
> [Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



