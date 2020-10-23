---
title: Enviar mensagens para um servidor MQTT utilizando a biblioteca de clientes Azure MQTT
description: Saiba como utilizar a biblioteca do Cliente MQTT para enviar mensagens a um corretor MQTT. Aprenda também a configurar o seu mXChip IoT DevKit para ser um cliente MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: fb8bf593568825793a1a205a2955599b16fa78cf
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151764"
---
# <a name="send-messages-to-an-mqtt-server"></a>Enviar mensagens para um servidor MQTT

Os sistemas internet of Things (IoT) lidam frequentemente com ligações intermitentes, de má qualidade ou lentas à Internet. MQTT é um protocolo de conectividade máquina-a-máquina (M2M), que foi desenvolvido com tais desafios em mente. 

A biblioteca de clientes MQTT usada aqui faz parte do projeto [Eclipse Paho,](https://www.eclipse.org/paho/) que fornece APIs para usar MQTT em vários meios de transporte.

## <a name="what-you-learn"></a>O que irá aprender

Neste projeto, aprende-se:
- Como utilizar a biblioteca do Cliente MQTT para enviar mensagens a um corretor MQTT.
- Como configurar o seu MXChip Iot DevKit como cliente MQTT.

## <a name="what-you-need"></a>O que precisa

Termine o [Guia de Início](./iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Tenha o seu DevKit ligado a Wi-Fi
* Preparar o ambiente de desenvolvimento

## <a name="open-the-project-folder"></a>Abra a pasta do projeto

1. Se o DevKit já estiver ligado ao computador, desligue-o.

2. Inicie o VS Code.

3. Ligue o DevKit ao seu computador.

## <a name="open-the-mqttclient-sample"></a>Abra a amostra MQTTClient

Expanda a secção **ARDUINO EXAMPLES** do lado esquerdo, navegue **para Exemplos para MXCHIP AZ3166 > MQTT,** e selecione **MQTTClient**. Uma nova janela do Código VS abre com uma pasta de projeto.

> [!NOTE]
> Também pode abrir o exemplo a partir da paleta de comando. Utilize `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) para abrir a paleta de comando, **digite Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Construa e carrequiva o esboço de Arduino para o DevKit

Tipo `Ctrl+P` (macOS: `Cmd+P` ) para correr `task device-upload` . Uma vez concluído o upload, o DevKit reinicia e executa o esboço.

![A screenshot mostra uma janela de pedido de comando que carrega e executa o esboço de Arduino.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Pode receber uma mensagem de erro "Error: AZ3166: Unknown package". Este erro ocorre quando o índice de pacote de placa não é atualizado corretamente. Para resolver este erro, consulte a [secção de desenvolvimento do IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

No Código VS, siga este procedimento para abrir e configurar o Monitor de Série:

1. Clique `COM[X]` na palavra na barra de estado para definir a porta COM certa com `STMicroelectronics` : Screenshot mostra Código de Estúdio Visual com electrónica ![ COM8 S T Micro selecionada.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Clique no ícone da ficha de alimentação na barra de estado para abrir o Monitor de Série: ![ O Screenshot mostra o resumo de Desbloqueio e o ícone da ficha de alimentação na barra de estado.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na barra de estado, clique no número que representa a Taxa Baud e defina-a para `115200` : Screenshot mostra a ![ definição da Taxa Baud no Código do Estúdio Visual.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

O Monitor em Série exibe todas as mensagens enviadas pelo esboço da amostra. O esboço liga o DevKit ao Wi-Fi. Uma vez que a ligação Wi-Fi seja bem sucedida, o esboço envia uma mensagem para o corretor MQTT. Depois disso, a amostra envia repetidamente duas mensagens "iot.eclipse.org" utilizando QoS 0 e QoS 1, respectivamente.

![A imagem mostra o Monitor em Série a exibir as mensagens enviadas pelo esboço.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou conecte-se utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Consulte também

* [Ligue ioT DevKit AZ3166 a Azure IoT Hub na nuvem](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake for a Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar o seu MXChip Iot DevKit como cliente MQTT e a usar a biblioteca do Cliente MQTT para enviar mensagens a um corretor MQTT, eis o próximo passo sugerido: visão geral do [acelerador de solução de monitorização remota Azure IoT](/azure/iot-suite/)