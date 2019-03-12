---
title: Enviar mensagens para um servidor MQTT com a biblioteca de cliente do Azure MQTT | Documentos da Microsoft
description: Utilize o DevKit como um cliente para enviar mensagens para um servidor MQTT
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 60520f5a72fd7e27d4ea64ac76511a00a727426e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552015"
---
# <a name="send-messages-to-an-mqtt-server"></a>Enviar mensagens para um servidor MQTT

Sistemas de Internet das coisas (IoT), muitas vezes, lidam com intermitente, de baixa qualidade ou lenta ligações à internet. MQTT é um protocolo de conectividade do máquina-a-máquina (M2M), que foi desenvolvido com tais desafios em mente. 

A biblioteca de cliente MQTT usada aqui é a parte a [Eclipse Paho](https://www.eclipse.org/paho/) projeto, que fornece APIs para a utilização MQTT ao longo de vários meios de transporte.

## <a name="what-you-learn"></a>O que irá aprender

Este projeto, vai aprender:
- Como utilizar a biblioteca de cliente de MQTT para enviar mensagens para um mediador de MQTT.
- Como configurar o seu MXChip Iot DevKit como um cliente MQTT.

## <a name="what-you-need"></a>Do que precisa

Concluir o [guia de introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Sua DevKit tiver ligado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

## <a name="open-the-project-folder"></a>Abra a pasta de projeto

1. Se o DevKit já está a ligar ao seu computador, desligue-lo.

2. Inicie o VS Code.

3. Ligar o DevKit ao seu computador.

## <a name="open-the-mqttclient-sample"></a>Abra o exemplo de MQTTClient

Expanda o lado esquerdo **exemplos de ARDUINO** secção, navegue até **exemplos para MXCHIP AZ3166 > MQTT**e selecione **MQTTClient**. Uma nova janela do VS Code é aberto com uma pasta de projeto.

> [!NOTE]
> Também é possível abrir o exemplo de paleta de comandos. Uso `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, escreva **Arduino**e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Criar e carregar o esboço de Arduino para o DevKit

Tipo `Ctrl+P` (macOS: `Cmd+P`) para executar `task device-upload`. Depois de concluído o carregamento, DevKit reinicia e executa o esboço.

![carregamento de dispositivo](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Poderá receber um "erro: AZ3166: Mensagem de erro do pacote desconhecido". Este erro ocorre quando o índice de pacote de quadro não é atualizado corretamente. Para resolver este erro, consulte a [secção do desenvolvimento de IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

No VS Code, siga este procedimento para abrir e configurar o Monitor de série:

1. Clique nas `COM[X]` word na barra de status para definir a porta COM correta com `STMicroelectronics`: ![porta de com de conjunto](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Clique no ícone de plug power na barra de status para abrir o Monitor de série: ![monitor de série](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na barra de status, clique no número que representa a taxa de transmissão e defini-lo `115200`: ![taxa de transmissão de conjunto](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

O Monitor Serial apresenta todas as mensagens enviadas pelo esboço de exemplo. O esboço liga-se a DevKit ao Wi-Fi. Assim que a ligação Wi-Fi for bem-sucedida, o esboço envia uma mensagem no Mediador MQTT. Depois disso, o exemplo envia repetidamente duas mensagens de "iot.eclipse.org" usando o QoS 0 e 1 de QoS, respectivamente.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemas e comentários

Se tiver problemas, consulte a [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou ligue-se com os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Ver também

* [Ligar a IoT DevKit AZ3166 ao IoT Hub do Azure na cloud](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Abane, abane para um Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu a configurar o seu MXChip Iot DevKit como um cliente MQTT e utilizar a biblioteca de cliente de MQTT para enviar mensagens para um mediador de MQTT, aqui estão os passos sugeridos seguintes:

* [Monitorização remota do IoT solution accelerator descrição geral do Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
