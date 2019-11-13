---
title: Conectar o simulador da Web do Raspberry Pi ao Hub IoT do Azure (Node. js)
description: Conecte o simulador da Web do Raspberry Pi ao Hub IoT do Azure para o Raspberry Pi para enviar dados para a nuvem do Azure.
author: wesmc7777
manager: philmea
keywords: simulador do Raspberry Pi, Azure IOT Raspberry Pi, Hub IOT do Raspberry Pi, Raspberry Pi enviar dados para a nuvem, Raspberry Pi para nuvem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954521"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Conectar o simulador online do Raspberry Pi ao Hub IoT do Azure (Node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com o simulador online do Raspberry Pi. Em seguida, você aprende como conectar diretamente o simulador de PI à nuvem usando o [Hub IOT do Azure](about-iot-hub.md).

Se você tiver dispositivos físicos, visite [conectar o Raspberry Pi ao Hub IOT do Azure](iot-hub-raspberry-pi-kit-node-get-started.md) para começar.

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

## <a name="what-you-do"></a>O que você faz

* Conheça os conceitos básicos do simulador online do Raspberry Pi.

* Crie um hub IoT.

* Registrar um dispositivo para PI em seu hub IoT.

* Execute um aplicativo de exemplo no PI para enviar dados de sensor simulados para o Hub IoT.

Conecte o Simulated Raspberry Pi a um hub IoT criado por você. Em seguida, você executa um aplicativo de exemplo com o simulador para gerar dados de sensor. Por fim, você envia os dados do sensor para o Hub IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a nova cadeia de conexão do dispositivo. Se você não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.

* Como trabalhar com o simulador online do Raspberry Pi.

* Como enviar dados de sensor para o Hub IoT.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Visão geral do simulador da Web do Raspberry Pi

Clique no botão para iniciar o simulador online do Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Iniciar o simulador do Raspberry Pi</a>

Há três áreas no simulador da Web.

1. Área de assembly-o circuito padrão é que um PI se conecta com um sensor BME280 e um LED. A área está bloqueada na versão de visualização, portanto, no momento, você não pode fazer a personalização.

2. Área de codificação – um editor de código online para você codificar com o Raspberry Pi. O aplicativo de exemplo padrão ajuda a coletar dados de sensor do sensor BME280 e envia para o Hub IoT do Azure. O aplicativo é totalmente compatível com dispositivos reais de PI. 

3. Janela de console integrado – mostra a saída do seu código. Na parte superior desta janela, há três botões.

   * **Executar** -execute o aplicativo na área de codificação.

   * **Redefinir** – redefina a área de codificação para o aplicativo de exemplo padrão.

   * **Dobrar/expandir** -no lado direito há um botão para você dobrar/expandir a janela do console.

> [!NOTE]
> O simulador da Web do Raspberry Pi agora está disponível na versão de visualização. Gostaríamos de ouvir sua voz no [Gitter chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). O código-fonte é público no [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Visão geral do simulador online PI](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Executar um aplicativo de exemplo no simulador de Web PI

1. Na área de codificação, verifique se você está trabalhando no aplicativo de exemplo padrão. Substitua o espaço reservado na linha 15 pela cadeia de conexão do dispositivo do Hub IoT do Azure.
1. 
   ![Substituir a cadeia de conexão do dispositivo](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Selecione **executar** ou digite `npm start` para executar o aplicativo.

Você deve ver a seguinte saída que mostra os dados do sensor e as mensagens que são enviadas para o Hub IoT ![dados de sensor de saída enviados do Raspberry Pi para o Hub IoT](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Ler as mensagens recebidas pelo seu hub

Uma maneira de monitorar as mensagens recebidas pelo Hub IoT do dispositivo simulado é usar as ferramentas de IoT do Azure para Visual Studio Code. Para saber mais, confira [usar as ferramentas de IOT do Azure para Visual Studio Code para enviar e receber mensagens entre o dispositivo e o Hub IOT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para obter mais maneiras de processar dados enviados pelo seu dispositivo, continue na próxima seção.

## <a name="next-steps"></a>Passos seguintes

Você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los para o Hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
