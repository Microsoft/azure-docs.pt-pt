---
title: Envie mensagens para um servidor MQTT usando biblioteca de clientes Azure MQTT
description: Saiba como usar a biblioteca do Cliente MQTT para enviar mensagens a um corretor MQTT. Aprenda também a configurar o seu mXChip IoT DevKit para ser um cliente MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 600e64ef5bc3329f0116359066bdcdaf42c13e2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733482"
---
# <a name="send-messages-to-an-mqtt-server"></a>Enviar mensagens para um servidor MQTT

Os sistemas internet of things (IoT) lidam frequentemente com ligações intermitentes, de má qualidade ou de internet lentas. MQTT é um protocolo de conectividade máquina-a-máquina (M2M), que foi desenvolvido com tais desafios em mente. 

A biblioteca de clientes MQTT utilizada aqui faz parte do projeto [Eclipse Paho,](https://www.eclipse.org/paho/) que fornece APIs para usar MQTT sobre vários meios de transporte.

## <a name="what-you-learn"></a>O que irá aprender

Neste projeto, aprende-se:
- Como utilizar a biblioteca do Cliente MQTT para enviar mensagens a um corretor MQTT.
- Como configurar o seu MXChip Iot DevKit como cliente MQTT.

## <a name="what-you-need"></a>Do que precisa

Termine o [Guia de Início de Partida](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Tenha o seu DevKit ligado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

## <a name="open-the-project-folder"></a>Abra a pasta do projeto

1. Se o DevKit já estiver ligado ao seu computador, desligue-o.

2. Inicie o VS Code.

3. Ligue o DevKit ao seu computador.

## <a name="open-the-mqttclient-sample"></a>Abra a amostra mQTTClient

Expandir a secção DE **EXEMPLOS ARDUINO** do lado esquerdo, navegar em **Exemplos para MXCHIP AZ3166 > MQTT,** e selecione **MQTTClient**. Uma nova janela vs Código abre com uma pasta de projeto.

> [!NOTE]
> Também pode abrir o exemplo a partir da paleta de comando. Utilize `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comando, digite **Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Construa e carregue o esboço arduino para o DevKit

Tipo `Ctrl+P` (macOS: `Cmd+P`) `task device-upload`para executar . Uma vez concluído o upload, o DevKit reinicia e executa o esboço.

![dispositivo-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Pode receber uma mensagem de erro "Error: AZ3166: Unknown package". Este erro ocorre quando o índice do pacote de placa não é atualizado corretamente. Para resolver este erro, consulte a [secção de desenvolvimento do IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

No Código VS, siga este procedimento para abrir e configurar o Monitor de Série:

1. Clique `COM[X]` na palavra na barra de estado `STMicroelectronics`para ![definir a porta COM direita com : set-com-porta](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Clique no ícone da ficha de alimentação ![na barra de estado para abrir o Monitor de Série: monitor de série](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na barra de estado, clique no número que representa `115200`a ![Taxa Baud e coloque-o em : taxa de set-baud](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

O Monitor de Série apresenta todas as mensagens enviadas pelo esboço da amostra. O esboço liga o DevKit ao Wi-Fi. Uma vez que a ligação Wi-Fi é bem sucedida, o esboço envia uma mensagem para o corretor MQTT. Depois disso, a amostra envia repetidamente duas mensagens "iot.eclipse.org" utilizando qoS 0 e QoS 1, respectivamente.

![produção em série](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou ligue-se utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Consulte também

* [Ligue IoT DevKit AZ3166 ao Hub Azure IoT na nuvem](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake for a Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar o seu MXChip Iot DevKit como cliente MQTT e usar a biblioteca do Cliente MQTT para enviar mensagens a um corretor MQTT, aqui estão os próximos passos sugeridos:

* [Visão geral do acelerador de solução de monitorização remota Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Ligue um dispositivo MXChip IoT DevKit à sua aplicação Central Azure IoT](/azure/iot-central/core/howto-connect-devkit)
