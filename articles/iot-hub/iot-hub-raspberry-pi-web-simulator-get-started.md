---
title: Conecte o simulador web Raspberry Pi ao Hub Azure IoT (Node.js)
description: Ligue o simulador web Raspberry Pi ao Hub Azure IoT para raspberry Pi enviar dados para a nuvem Azure.
author: wesmc7777
manager: philmea
keywords: framboesa pi simulator, azure iot raspberry pi, raspberry pi iot hub, raspberry pi enviar dados para a nuvem, framboesa pi para a nuvem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954521"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Conecte o simulador online Raspberry Pi ao Hub Azure IoT (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começa-se por aprender o básico de trabalhar com o simulador online Raspberry Pi. Em seguida, aprende-se a ligar perfeitamente o simulador Pi à nuvem utilizando o [Hub Azure IoT](about-iot-hub.md).

Se tiver dispositivos físicos, visite [o Connect Raspberry Pi até ao Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) para começar.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>O que faz

* Saiba o básico do simulador online Raspberry Pi.

* Criar um centro de ioT.

* Registe um dispositivo para pi no seu centro ioT.

* Faça uma aplicação de amostra em Pi para enviar dados de sensores simulados para o seu centro IoT.

Ligue raspberry Pi simulado a um centro ioT que você cria. Em seguida, executa uma aplicação de amostra com o simulador para gerar dados de sensores. Finalmente, envia os dados do sensor para o seu centro ioT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub Azure IoT e obter a sua nova cadeia de ligação ao dispositivo. Se não tiver uma conta Azure, [crie uma conta de teste Azure gratuita](https://azure.microsoft.com/free/) em poucos minutos.

* Como trabalhar com o simulador online Raspberry Pi.

* Como enviar dados de sensores para o seu centro ioT.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Visão geral do simulador web raspberry Pi

Clique no botão para lançar o simulador online Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Iniciar O Simulador de Framboesa Pi</a>

Há três áreas no simulador da web.

1. Área de montagem – o circuito padrão é um Pi que estabelece ligação a um sensor BME280 e a um LED. A área está bloqueada na versão de pré-visualização, por isso, atualmente não é possível personalizar.

2. Área de codificação – um editor de código online para codificar com o Raspberry Pi. A aplicação de exemplo padrão ajuda a recolher dados do sensor BME280 e envia-os para o Hub IoT. A aplicação é totalmente compatível com dispositivos Pi reais. 

3. Janela de consola integrada – mostra a saída do código. Na parte superior desta janela, existem três botões.

   * **Executar** – executa a aplicação na área de codificação.

   * **Repor** – repõe a área de codificação para a aplicação de exemplo padrão.

   * **Dobrar/Expandir** – no lado direito, há um botão para dobrar/expandir a janela de consola.

> [!NOTE]
> O simulador web Raspberry Pi já está disponível na versão de pré-visualização. Gostaríamos de ouvir a sua voz no [Gitter Chatroom.](https://gitter.im/Microsoft/raspberry-pi-web-simulator) O código fonte é público no [GitHub.](https://github.com/Azure-Samples/raspberry-pi-web-simulator)

![Visão geral do simulador on-line Pi](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Executar um exemplo de aplicação no simulador Web Pi

1. Na área de codificação, verifique que está a trabalhar na aplicação de exemplo predefinida. Substitua o marcador de posição na Linha 15 pela cadeia de ligação do dispositivo do hub IoT do Azure.
1. 
   ![Substituir a cadeia de ligação do dispositivo](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Selecione `npm start` **Executar** ou digitar para executar a aplicação.

Deve ver a seguinte saída que mostra os dados do sensor e ![as mensagens que são enviadas para o seu hub IoT Output - dados de sensores enviados de Raspberry Pi para o seu hub IoT](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Leia as mensagens recebidas pelo seu centro

Uma forma de monitorizar as mensagens recebidas pelo seu hub IoT a partir do dispositivo simulado é utilizar as ferramentas Azure IoT para código de estúdio visual. Para saber mais, consulte [Use Ferramentas Azure IoT para o Código do Estúdio Visual para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para mais formas de processar os dados enviados pelo seu dispositivo, continue na secção seguinte.

## <a name="next-steps"></a>Passos seguintes

Executou uma aplicação de amostra para recolher dados de sensores e enviá-lo para o seu centro ioT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
