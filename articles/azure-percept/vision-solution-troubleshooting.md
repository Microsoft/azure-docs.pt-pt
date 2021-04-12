---
title: Problemas de resolução de problemas com módulos Azure Percept Vision e vision
description: Obtenha dicas de resolução de problemas para algumas das questões mais comuns encontradas nas experiências de prototipagem de IA da visão
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074697"
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

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Página IoT Edge para dispositivo selecionado que mostre o conteúdo do separador dos módulos." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Eliminar um dispositivo

1. Aceda ao [Portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Clique no ícone **Iot Hub.**

1. Selecione o Hub IoT ao qual o seu dispositivo-alvo está ligado.

1. Selecione **IoT Edge** e verifique a caixa ao lado do ID do seu dispositivo alvo. Clique no ícone do caixote do lixo para eliminar o seu dispositivo.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Eliminar ícone realçado na página inicial do IoT Edge.":::

## <a name="eye-module-troubleshooting-tips"></a>Dicas de resolução de problemas do módulo ocular

### <a name="check-the-runtime-status-of-azureeyemodule"></a>Verifique o estado de funcionamento do azureeyemodule

Se houver algum problema com **o WebStreamModule**, certifique-se de que **o azureeyemodule**, que lida com a inferencing do modelo de visão, está em funcionamento. Para verificar o estado de funcionamento, vá ao [portal Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e navegue para **todos os recursos**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Clique no **separador Módulos** para ver o estado de funcionação de todos os módulos instalados.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Ecrã de estado de funcionaamento do módulo do dispositivo." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Se o estado de funcionamento do **azureeyemodule** não estiver listado como **funcionamento,** clique em **Definir módulos**  ->  **azureeyemodule**. Na página **Definições** do Módulo, defina o **Estado Desejado** para **executar** e clique em **Atualizar**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Ecrã de configuração de configuração do módulo.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>Atualizar TelemetriaIntervalNeuralNetworkMs

Se encontrar o seguinte erro de limitação da contagem, o valor da TelemetriaIntervalNeuralNetworkMs nas definições gémeas do módulo azureeyemodule terá de ser atualizado.

|Mensagem de Erro|
|------|
|O número total de mensagens no IotHub 'xxxxxx' excedeu a quota atribuída. Max permitiu a contagem de mensagens: '8000', contagem de mensagens atuais: 'xxxx'. As operações de envio e receção estão bloqueadas para este centro até ao dia seguinte da UTC. Considere aumentar as unidades para este centro aumentar a quota.|

TelemetriaIntervalNeuralNetworkMs determina com que frequência enviar mensagens (em milissegundos) da rede neural. As subscrições do Azure têm um número limitado de mensagens por dia, dependendo do seu nível de subscrição. Se se encontrar bloqueado por ter enviado demasiadas mensagens, aumente este número para um número mais elevado. 12000 (ou seja, uma vez a cada 12 segundos) dar-lhe-á uma boa rodada de 7200 mensagens por dia, que está abaixo do limite de 8000 mensagens para a subscrição gratuita.

Para atualizar o valor da TelemetriaIntervalNeuralNetworkMs, siga estes passos:

1. Faça login no [portal Azure](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) e abra **todos os recursos**.

1. Na página **De Todos os recursos,** clique no nome do IoT Hub que foi a provisionado ao seu devkit durante a experiência de configuração.

1. No lado esquerdo da página IoT Hub, clique na **Borda IoT** em **Gestão Automática de Dispositivos**. Na página de dispositivos IoT Edge, encontre o ID do dispositivo do seu devkit. Clique no ID do dispositivo do seu devkit para abrir a sua página de dispositivo IoT Edge.

1. Selecione **a azureeyemodule** sob o **separador Módulos.**

1. Na página azureeyemodule, abra **o Módulo Identidade Twin**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Screenshot da página do módulo." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Desloque-se até as **propriedades**. Note que as propriedades "Running" e "Logging" não estão ativas neste momento.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Screenshot de propriedades gémeas do módulo." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Atualize o valor **de TelemetriaIntervalNeuralNetworkMs** conforme desejado e clique no ícone **Guardar.**

## <a name="view-device-rtsp-video-stream"></a>Ver fluxo de vídeo RTSP do dispositivo

Veja o fluxo de vídeo RTSP do seu dispositivo no [Azure Percept Studio](./how-to-view-video-stream.md) ou [no leitor de mídia VLC](https://www.videolan.org/vlc/index.html).

Para abrir o fluxo RTSP no leitor de mídia VLC, vá ao stream de rede **Media**  ->  **Open**  ->  **rtsp://[endereço IP do dispositivo]/resultado**.

## <a name="next-steps"></a>Passos seguintes

Consulte o [guia geral de resolução de problemas](./troubleshoot-dev-kit.md) para obter mais informações sobre a resolução de problemas do seu Azure Percept DK.