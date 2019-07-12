---
title: Simulated Raspberry Pi para cloud (node. js) - simulador de web de ligar o Raspberry Pi hub IoT do Azure | Documentos da Microsoft
description: Ligar o simulador de web Raspberry Pi ao IoT Hub do Azure para o Raspberry Pi enviar dados para a cloud do Azure.
author: wesmc7777
manager: philmea
keywords: simulador de raspberry pi, o azure iot raspberry pi, hub de iot raspberry pi, enviar dados para a cloud de raspberry pi, raspberry pi com a nuvem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 9af2f0860c415ddb701e24ed8a698fae36d42e1f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838712"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Ligar o simulador online de Raspberry Pi ao IoT Hub do Azure (node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começará aprender as noções básicas de trabalhar com o simulador online de Raspberry Pi. Em seguida, saiba como se conecte o simulador de instalador de plataforma para a cloud, utilizando [IoT Hub do Azure](about-iot-hub.md).

Se tiver dispositivos físicos, visite [ligar o Raspberry Pi hub IoT do Azure](iot-hub-raspberry-pi-kit-node-get-started.md) para começar a utilizar.

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

## <a name="what-you-do"></a>O que fazer

* Aprenda as noções básicas de simulador online de Raspberry Pi.

* Crie um hub IoT.

* Registe um dispositivo para o instalador de plataforma do seu hub IoT.

* Execute uma aplicação de exemplo no instalador de plataforma para enviar dados de sensor simulado ao seu hub IoT.

Ligue-se simulado Raspberry Pi para um hub IoT que criou. Em seguida, executar um aplicativo de exemplo com o simulador para gerar dados de sensor. Por fim, que envia os dados de sensor ao seu hub IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a cadeia de ligação do novo dispositivo. Se não tiver uma conta do Azure, [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.

* Como trabalhar com o simulador online de Raspberry Pi.

* Como enviar dados de sensor ao seu hub IoT.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Descrição geral do simulador de web Raspberry Pi

Clique no botão para iniciar o simulador online de Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Iniciar o simulador de Raspberry Pi</a>

Existem três áreas no simulador web.

1. Área de Assembly - o circuito de padrão é que um Pi estabelece uma ligação com um sensor BME280 e um LED. A área está bloqueada na versão de pré-visualização por isso, atualmente não é possível fazer a personalização.

2. Codificação área - um editor de código online para ao código Raspberry PI. O aplicativo de exemplo do padrão ajuda a recolher dados de sensores de BME280 sensor e envia ao seu IoT Hub do Azure. O aplicativo é totalmente compatível com dispositivos reais do instalador de plataforma. 

3. Janela de consola integrada - mostra a saída do seu código. Na parte superior desta janela, existem três botões.

   * **Executar** -executar a aplicação na área de codificação.

   * **Repor** -repor a área de codificação para o aplicativo de exemplo do padrão.

   * **Subconjuntos de validação/Expand** -no lado direito, há um botão para expandir/subconjuntos de validação a janela da consola.

> [!NOTE]
> Simulador web Raspberry Pi está agora disponível em versão de pré-visualização. Gostaríamos de ouvir a sua voz no [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). O código-fonte é público na [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Descrição geral do simulador online do instalador de plataforma](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Executar um exemplo de aplicação no simulador do instalador de plataforma web

1. Na área de codificação, certifique-se de que está a trabalhar a aplicação de exemplo do padrão. Substitua o marcador de posição na linha 15 a cadeia de ligação do dispositivo de hub IoT do Azure.
1. 
   ![Substitua a cadeia de ligação do dispositivo](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Selecione **execute** ou escreva `npm start` para executar a aplicação.

Deverá ver o resultado seguinte que mostra os dados de sensor e as mensagens que são enviadas ao seu hub IoT ![saída - dados de sensor enviados do Raspberry Pi ao seu hub IoT](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Ler as mensagens recebidas pelo hub

Uma forma para monitorizar mensagens recebidas pelo hub IoT do dispositivo simulado é usar as ferramentas de IoT do Azure para Visual Studio Code. Para obter mais informações, consulte [Utilize ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para obter mais formas de processar os dados enviados pelo seu dispositivo, avance para a secção seguinte.

## <a name="next-steps"></a>Passos Seguintes

Executar uma aplicação de exemplo para recolher dados de sensor e enviá-lo ao seu hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
