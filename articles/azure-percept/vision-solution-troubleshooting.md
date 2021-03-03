---
title: Problemas de resolução de problemas com módulos Azure Percept Vision e vision
description: Obtenha dicas de resolução de problemas para algumas das questões mais comuns encontradas nas experiências de prototipagem de IA da visão
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663518"
---
# <a name="vision-solution-troubleshooting"></a>Resolução de problemas da solução de visão

Consulte as seguintes orientações para informações sobre soluções de visão sem código no Azure Percept Studio.

## <a name="delete-a-vision-project"></a>Excluir um projeto de visão

1. Aceda a https://www.customvision.ai/projects.

1. Paire sobre o projeto que você gostaria de apagar e clicar no ícone do caixote do lixo para apagar o projeto.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Página de projetos em Visão Personalizada com ícone de exclusão realçado.":::

## <a name="check-which-modules-are-on-a-device"></a>Verifique quais os módulos que estão num dispositivo

1. Aceda ao [Portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Clique no ícone **Iot Hub.**

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Página inicial do portal Azure com ícone Iot Hub em destaque." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Selecione o Hub IoT ao qual o seu dispositivo-alvo está ligado.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Lista de Hubs IoT.":::

1. Selecione **IoT Edge** e clique no seu dispositivo sob o **separador ID** do dispositivo.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="Página inicial do IoT Edge.":::

1. Os módulos do seu dispositivo serão listados no **separador Módulos.**

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Página Iot Edge para dispositivo selecionado que mostra o conteúdo do separador dos módulos." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Eliminar um dispositivo

1. Aceda ao [Portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Clique no ícone **Iot Hub.**

1. Selecione o Hub IoT ao qual o seu dispositivo-alvo está ligado.

1. Selecione **IoT Edge** e verifique a caixa ao lado do ID do seu dispositivo alvo. Clique no ícone do caixote do lixo para eliminar o seu dispositivo.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Eliminar ícone realçado na página inicial do Iot Edge.":::

## <a name="eye-module-troubleshooting-tips"></a>Dicas de resolução de problemas do módulo ocular

Se houver algum problema com **o WebStreamModule**, certifique-se de que **o azureeyemodule**, que faz o modelo de visão inferencing, está em execução. Para verificar o estado de funcionamento, vá ao [portal Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e navegue para **todos os recursos**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Clique no **separador Módulos** para ver o estado de funcionação de todos os módulos instalados.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Ecrã de estado de funcionaamento do módulo do dispositivo." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Se o estado de funcionamento do **azureeyemodule** não estiver listado como **funcionamento,** clique em **Definir módulos**  ->  **azureeyemodule**. Na página **Definições** do Módulo, defina o **Estado Desejado** para **executar** e clique em **Atualizar**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Ecrã de configuração de configuração do módulo.":::

## <a name="view-device-rtsp-video-stream"></a>Ver fluxo de vídeo RTSP do dispositivo

Veja o fluxo de vídeo RTSP do seu dispositivo no [Azure Percept Studio](./how-to-view-video-stream.md) ou [no leitor de mídia VLC](https://www.videolan.org/vlc/index.html).

Para abrir o stream RTSP no leitor de mídia VLC, vá ao **Media**  ->  **Open stream**  ->  **rtsp://[endereço IP do dispositivo]/resultado**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [guia geral de resolução de problemas](./troubleshoot-dev-kit.md) para obter mais informações sobre a resolução de problemas do seu Azure Percept DK.